<a name="top"></a>

* [VM Configuration](#vm_config)
* [Ceph Key and Pools](#key_pools)
* [General Setup Steps](#gen_setup)
     * [Cephx Key](#cephx_key)
     * [Default Pools](#pools)
* [Radosgw for Ceph Firefly](#radosgw-for-ceph-firefly)
     * [Apache web server](#firefly_apache)
     * [radosgw configuration](#firefly_config)
     * [Automated Setup](#automated)
* [Radosgw for Ceph Hammer](#radosgw-for-ceph-hammer)
     * [Civetweb web server](#hammer_civetweb)
     * [radosgw configuration](#hammer_config)
* [Radosgw for Ceph Jewel (v.10.2.x)](#radosgw-for-ceph-jewel)
* [Testing the Radosgw](#testing)
* [Debugging tips](#debug)
* [Useful Links](#useful_links)

##<a name="vm_config"></a>VM Configuration

The Radosgw VM should have 2 interfaces: an interface on the Ceph network, and a public interface for the web server to listen on.

######Public Interface Config

	TYPE=Ethernet
	BOOTPROTO=static
	DEVICE=ens3
	NAME=ens3
	ONBOOT=yes
	IPADDR=129.10.3.x
	NETMASK=255.255.255.128
	GATEWAY=129.10.3.1
	DNS1=8.8.8.8
     
######Ceph Interface Config

	TYPE=Ethernet
	BOOTPROTO=static
	IPADDR=192.168.28.x
	NAME=ens5
	DEVICE=ens5
	ONBOOT=yes


[top](#top)

***

##<a name="gen_setup"></a>General Setup Steps
These steps should happen regardless of what version of Ceph you are using.

####Disable SELinux

On the radosgw VM, run:

     # setenforce 0
     # getenforce

Also edit /etc/selinux/config so that the SELINUX line looks like this:

     SELINUX=permissive

####<a name="cephx_key"></a>Cephx authentication key
**IMPORTANT:** Keys are created on the **ceph admin node**; in the NU deployment this is the "Fujitsu Remote Management" server.
Log into the Ceph admin node and run this command, changing "production" to the appropriate name for your gateway:

     # ceph auth get-or-create "client.radosgw.production" mon 'allow r' osd 'allow rwx' > "/etc/ceph/client.radosgw-production.key"

####<a name="pools"></a>Default Pools
**IMPORTANT:** Pools are created on the **ceph admin node**; in the NU deployment this is the "Fujitsu Remote Management" server.

Use [this calculator](http://ceph.com/pgcalc/) to calculate the number of placement groups for each pool.  Choose "Rados Gateway Only" from the dropdown and change "Target PGs Per OSD" to 100 on each line. The calculator recommends 32 (minimum pg number) for all the default radosgw pools except .rgw.buckets, for which it suggests 4096.

Per George, we should namespace pools, so instead of `.rgw.buckets` create `.production.rgw.buckets`.  However, I ran into authentication problems if I did this with the 'user' pools.  So the set of pools I created was:

    .production.intent-log
    .production.log
    .production.rgw.buckets
    .production.rgw.buckets.extra
    .production.rgw.buckets.index
    .production.rgw.control
    .production.rgw.gc
    .production.rgw.root
    .production.usage
    .users
    .users.email
    .users.swift
    .users.uid

######Create a Pool
The command to create a pool on the CD10K is:

     # vsm_cli cd10k create-pool -d performance -r 3 -n {#_OF_PGs} {POOL_NAME}

######Deleting a Pool

If you accidentally create a pool, delete it like this:

     ceph osd pool delete <pool_name> <pool_name> --yes-i-really-really-mean-it

Make sure you really mean it!

*(Fun fact: it is possible to name a pool "[pool_name]".)*

[top](#top)

***
##Radosgw for Ceph Firefly 
Ceph Firefly is v. 0.80.x which corresponds to Red Hat Ceph 1.2.3

####Enable repos
     # subscription-manager repos --enable=rhel-7-server-rh-common-rpms

####Configure hostname

     # hostnamectl set-hostname production-radosgw.moc.ne.edu

Also add the IP address and hostname to `/etc/hosts` /:
     
     # 129.10.3.x     production-radosgw.moc.ne.edu production-radosgw
   

####<a name="firefly_apache"></a>Install and configure Apache web server

     # yum install httpd

######httpd.conf
Make the following changes to `/etc/httpd/conf/httpd.conf`\:

* Change `Listen 80` to specify the public IP of gateway.
     
    `Listen 129.10.3.x:80`

* Change the `ServerName` line to specify the fully qualified domain name of the radosgw (output of `hostname -f`).
     
     `ServerName production-radosgw.moc.ne.edu`

######rgw.conf
Create the file `/etc/httpd/conf.d/rgw.conf` with the following content.  Fill in the appropriate public IP address.  Comment/uncomment the appropriate lines for TCP or UDS (in RHEL 7.x, use UDS) :

     /etc/httpd/conf.d/rgw.conf

     <VirtualHost 129.10.3.x:80>

     ServerName production-radosgw.moc.ne.edu

     DocumentRoot /var/www/html

     ErrorLog /var/log/httpd/rgw_error.log
     CustomLog /var/log/httpd/rgw_access.log combined
     #LogLevel debug

     RewriteEngine On
     RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization},L]

     SetEnv proxy-nokeepalive 1

     # Use this setting for localhost TCP in RHEL6, CentOS6, or other OS with Apache before 2.4.9
     #ProxyPass / fcgi://localhost:9000/

     # Use this setting for Unix Domain Sockets in RHEL7, CentOS7, or other OS with Apache 2.4.9 or greater  
     ProxyPass / unix:///var/run/ceph/ceph.radosgw.production.fastcgi.sock|fcgi://localhost:9000/

     </VirtualHost>

######FastCGI script
Create the file `/var/www/html/s3gw.fcgi` with the following content.  

     #!/bin/sh
     exec /usr/bin/radosgw -c /etc/ceph/ceph.conf -n client.radosgw.production

####Install radosgw
Install the radosgw package
     
     # yum install ceph-radosgw -y

If using a federated configuration, you should also install radosgw-agent

     # yum install radosgw-agent -y

####<a name="firefly_config"></a>Configure radosgw
Create the file `/etc/ceph/ceph.conf` with the following contents.
These values are for the Fujitsu Ceph Storage node; for a different setup, make sure to use the correct value for mon_initial_members and mon_host, as well as the correct client names and keyrings.  Also comment/uncomment the appropriate sections for TCP or UDS depending on the Apache configuration above.

	[global]
	mon_initial_members = storage1,storage2,storage3
	mon_host = 192.168.28.11:6789,192.168.28.12:6789,192.168.28.13:6789
	auth_cluster_required = cephx
	auth_service_required = cephx
	auth_client_required = cephx
	#debug ms=1
	#debug rgw=20

	[client.admin]
	keyring=/etc/ceph/keyring.admin

	[client.radosgw.production]
	host = production-radosgw
	keyring = /etc/ceph/client.production-radosgw.key
	log file = /var/log/radosgw/client.radosgw.production.log
	# Select the appropriate config according to the FCGI options in /etc/httpd/conf.d/rgw.conf
	# 
	# For localhost TCP:
	#rgw socket path = ""
	#rgw frontends = fastcgi socket_port=9000 socket_host=0.0.0.0
	#
	# For Unix Domain Sockets:
	rgw socket path = /var/run/ceph/ceph.radosgw.production.fastcgi.sock
	#
	# Set rgw print continue = false to avoid issues with PUT operations
	rgw print continue = false

####Add keyrings to radosgw
Copy the files with the admin keyring and the radosgw keyring from the ceph admin node to `/etc/ceph/` on the rados gatway.  Make sure the keyring file names match what it is in `/etc/ceph/ceph.conf`.

####Configure File/Directory Permissions
Run the following series of commands to set up the correct file permissions\:

     chown apache:apache /var/www/html/s3gw.fcgi
     chmod +x /var/www/html/s3gw.fcgi

     mkdir -p /var/lib/ceph/radosgw/production
     touch /var/log/radosgw/client.radosgw.production.log
     chown apache:apache /var/log/radosgw/client.radosgw.production.log
     mkdir -p /var/run/ceph
     chown -R apache:apache /var/run/ceph

You will need to run `chown -R apache:apache /var/run/ceph` every time you restart the radosgw service, because restarting it recreates the socket files and returns them to being owned by root.  This prevents apache from accessing them until you make apache the owner.


####Disable requiretty
Edit /etc/sudoers (using the command `# visudo`) and comment out this line:

     Defaults    requiretty

####Start Apache and Radosgw

     # systemctl start httpd
     # systemctl start ceph-radosgw

[top](#top)

***

####<a name="automated"></a>Automated Setup for Firefly
A tarball containing scripts and various template files for an automated install of Firefly on RHEL can be found here:

[radosgw_setup.tar](https://github.com/CCI-MOC/moc/blob/8f92c077641dd54c1cd93b7096f890fb8dc4098f/scripts/radosgw_setup.tar)

Please read the README and make sure you fill out the configuration section of the script properly.

This should be run as root on the VM after RHEL is installed and the correct networking is set up.

[top](#top)

***

##Radosgw for Ceph Hammer 
Ceph Hammer is v.0.94.x which corresponds to Red Hat Ceph 1.3

####<a name="hammer_repos"></a>Enable repos
     # subscription-manager repos --enable=rhel-7-server-rh-common-rpms

Create `/etc/yum.repos.d/ceph.repo` with the following content:

	[ceph]
	name=Ceph packages for $basearch
	baseurl=http://download.ceph.com/rpm-hammer/rhel7/$basearch
	enabled=1
	priority=2
	gpgcheck=1
	type=rpm-md
	gpgkey=http://download.ceph.com/keys/release.asc

	[ceph-noarch]
	name=Ceph noarch packages
	baseurl=http://download.ceph.com/rpm-hammer/rhel7/noarch
	enabled=1
	priority=2
	gpgcheck=1
	type=rpm-md
	gpgkey=http://download.ceph.com/keys/release.asc

	[ceph-source]
	name=Ceph source packages
	baseurl=http://download.ceph.com/rpm-hammer/rhel7/SRPMS
	enabled=0
	priority=2
	gpgcheck=1
	type=rpm-md
	gpgkey=http://download.ceph.com/keys/release.asc


####Configure hostname

     # hostnamectl set-hostname production-radosgw.moc.ne.edu

Also add the IP address and hostname to `/etc/hosts` /:
     
     # 129.10.3.x     production-radosgw.moc.ne.edu  production-radosgw

####Install radosgw
Install the radosgw package
     
     # yum install ceph-radosgw -y

If using a federated configuration, you should also install radosgw-agent

     # yum install radosgw-agent -y

####<a name="hammer_config"></a>Configure radosgw
Create the file `/etc/ceph/ceph.conf` with the following contents.
These values are for the Fujitsu Ceph Storage node; for a different setup, make sure to use the correct value for mon_initial_members and mon_host, as well as the correct client names and keyrings.  Also comment/uncomment the appropriate sections for TCP or UDS (see Configuring Apache).

	[global]
	mon_initial_members = storage1,storage2,storage3
	mon_host = 192.168.28.11:6789,192.168.28.12:6789,192.168.28.13:6789
	auth_cluster_required = cephx
	auth_service_required = cephx
	auth_client_required = cephx
	#debug ms=1
	#debug rgw=20
	#debug civetweb=10

	[client.admin]
	keyring=/etc/ceph/keyring.admin

	[client.radosgw.production]
	host = production-radosgw
	keyring = /etc/ceph/client.production-radosgw.key
	log file = /var/log/radosgw/client.radosgw.production.log
	### Civetweb
	rgw frontends = "civetweb port=80"
	# Set rgw print continue = false to avoid issues with PUT operations
	rgw print continue = false

Copy the files with the admin keyring and the radosgw keyring from the ceph admin node to `/etc/ceph/` on the rados gatway.

####<a name="hammer_civetweb"></a>CivetWeb
The Hammer Radosgw uses a lightweight web server called CivetWeb by default, although the documentation has not been updated to reflect this.  (There is a Ceph issue for this update [here](http://tracker.ceph.com/issues/12568).)

Hammer can also be configured to use Apache.  In that case, you should do the same Apache configuration steps described above in the instructions for Firefly.  In `/etc/ceph/ceph.conf`, replace the line `rgw frontends = "civetweb port=80"` with 

     rgw frontends=fastcgi

and follow the Firefly instructions for setting up Apache and configuring file permissions.

Civetweb starts automatically with ceph-radosgw, but if using Apache you need to run `# systemctl start httpd` before starting ceph-radosgw.  


[top](#top)

***

##Changing the default zone pools

Because we namespaced the pools, we need to configure the radosgw to write things into the correct pools.  A non-federated Ceph cluster will use the "default" region and zone, but we need to specify the pool names in the default zone configuration.

Create the following json file (call it `default_zone_production.json` for example):

	{ "domain_root": ".production.rgw",
	  "control_pool": ".production.rgw.control",
	  "gc_pool": ".production.rgw.gc",
	  "log_pool": ".production.log",
	  "intent_log_pool": ".production.intent-log",
	  "usage_log_pool": ".production.usage",
	  "user_keys_pool": ".users",
	  "user_email_pool": ".users.email",
	  "user_swift_pool": ".users.swift",
	  "user_uid_pool": ".users.uid",
	  "system_key": { "access_key": "", "secret_key": ""},
	  "placement_pools": [
	      {  "key": "default-placement",
	         "val": { "index_pool": ".production.rgw.buckets.index",
	                  "data_pool": ".production.rgw.buckets"}
	      }
	    ]
	  }

Then run:

     $ radosgw-admin zone set --infile=default_zone_production.json
     $ sudo systemctl restart ceph-radosgw

[top](#top)

***

##Radosgw for Ceph Jewel 
Ceph Jewel is v.10.2.x which corresponds to Red Hat Ceph 2

Something to note - as of Jewel the radosgw service unit file now includes the hostname, so when you start/stop the service you call it like this:

     # systemctl start ceph-radosgw@<hostname>

Example:

     # hostname -s
     e1-radosgw-01
     # systemctl start ceph-radosgw@e1-radosgw-01

Also, I got the following error when doing the steps to convert the keystone SSL keys:

     # openssl x509 -in /etc/keystone/ssl/certs/ca.pem -pubkey | certutil -A -d /tmp/nss -n ca -t "TCu, Cu, Tuw"
     Notice: Trust flag u is set automatically if the private key is present.
     certutil: unable to decode trust string: SEC_ERROR_INVALID_ARGS: security library: invalid arguments.

But, the .db files were still created in /tmp/nss and they worked, so I guess it is not an issue?
   
Links to useful Red Hat docs:
[Ceph Object Gateway Guide for RHEL](https://access.redhat.com/documentation/en/red-hat-ceph-storage/2/paged/object-gateway-guide-for-red-hat-enterprise-linux/)
[Configure Radosgw to use Keystone](https://access.redhat.com/documentation/en/red-hat-ceph-storage/2/paged/using-keystone-to-authenticate-ceph-object-gateway-users/)


##Testing

(This section mostly reproduces the instructions in the [Ceph Documentation](http://docs.ceph.com/docs/master/radosgw/config/) but with some additional notes/comments)

For more information about using the RADOS gateway to do operations, see [[Talking to the RADOS Gateway]]

####User Setup
Create a gateway user and a swift subuser with a swift secret key:

     $ radosgw-admin user create --uid=testuser --display-name="Test User" --access=full
     $ radosgw-admin subuser create --uid=testuser --subuser=testuser:swift --access=full
     $ radosgw-admin key create --subuser=testuser:swift --key-type=swift --gen-secret

In Firefly, it is possible for the user secret key and the swift secret key to have special characters in them, which are automatically prefaced by escape characters.  If you use these keys with Python later, you need to remove the escape characters when you put the key into a Python string.  To make things easier, it is recommended to keep generating keys until you get one that doesn't have escape characters.  In Hammer, this is not a problem - the keys do not contain escape characters.

To view a user's info and keys:

     $ radosgw-admin user info --uid=testuser

To generate a new user access key: 

     $ radosgw-admin key create --uid=testuser --gen-key

To delete an unwanted user access key (for {ACCESS_KEY} use the access key corresponding to the key you want to delete):

     $ radosgw-admin key rm --uid=testuser --access-key={ACCESS_KEY}

To generate a new swift secret key you just run the same command as to generate the first one, and the old one will be overwritten.

######S3 API

Install python-boto to use the S3 API
     
     $ sudo yum install python-boto

Create and run the following python script `s3test.py`, substituting the correct user and hostname info:

	import boto
	import boto.s3.connection
	access_key = {ACCESS_KEY} 
	secret_key = {SECRET_KEY}
	conn = boto.connect_s3(
	aws_access_key_id = access_key,
	aws_secret_access_key = secret_key,
	host = 'production-radosgw.moc.ne.edu',
	is_secure=False,
	calling_format = boto.s3.connection.OrdinaryCallingFormat(),
	)
	bucket = conn.create_bucket('test-bucket')
	#conn.delete_bucket('test-bucket')
	for bucket in conn.get_all_buckets():
	        print "{name}\t{created}".format(
	                name = bucket.name,
	                created = bucket.creation_date,
	)

Running `$ python s3test.py` should create a bucket called `test-bucket`, then list all buckets for the user with this key.  Uncomment the `conn.delete_bucket` line to delete a bucket (but make sure you really want to delete the bucket).

######Swift Interface
Install the following utilities:
     
     # yum install python-swiftclient

If when trying the swift interface you get an error about ssl_match_hostname, install the following backport:

     # yum install python-backports.ssl_match_hostname

The swift API is accessed like this:

     $ swift -A http://{IP ADDRESS}/auth/1.0 -U {SUBUSER} -K {SWIFT_SECRET_KEY} {COMMAND}

Examples:

List all buckets for user testuser:

    $ swift -A http://12.34.56.78/auth/1.0 -U testuser:swift -K pretendthisisaswiftsecretkey list

Create a bucket called testing-bucket:

    $ swift -A http://12.34.56.78/auth/1.0 -U testuser:swift -K pretendthisisaswiftsecretkey post testing-bucket

Delete the bucket testing-bucket:

    $ swift -A http://12.34.56.78/auth/1.0 -U testuser:swift -K pretendthisisaswiftsecretkey delete testing-bucket

***

##Open port 80
By default port 80 will only accept connections from localhost.  This is fine for setup/debugging but once everything works (i.e. you have gotten all the steps in [Testing](#testing) to work on the localhost), open the port so clients can connect.

     # firewall-cmd --zone=public --add-port=80/tcp --permanent
     # firewall-cmd --reload

Note: you can use any port for radosgw, just replace 80 with the port you are using in `/etc/ceph/ceph.conf` if using civetweb, or `httpd.conf` and 'rgw.conf` if using apache.  However, if the port is not 80, the clients will have to know what port to use.

At this point it's a good idea to run through some of the testing steps from a client also just to make sure your web server is really working.

####<a name="debug"></a>Debugging Tips

######General

* Radosgw logs are located at the path specified in the radosgw section of `/etc/ceph/ceph.conf`.  To increase the log level, uncomment the line `debug rgw=20` in `/etc/ceph/conf` and restart ceph-radosgw.

* The command `ceph df` will show you pool usage so you can check that bucket objects are going into the pools you expect.

* Make sure to update the zone information before creating any users or buckets; if you change the zone information after creating things, the user may not be able to authenticate and/or access their buckets. 

######If using Apache (Firefly or Hammer)
* There are rgw-specific Apache logs located at the path specified in `/etc/httpd/conf.d/rgw.conf`.  To increase the log level for debugging, uncomment the line `LogLevel debug` and restart httpd.

* Whenever you restart the ceph-radosgw, it recreates the socket files in /var/run/ceph, and the new ones are owned by root, which prevents apache from accessing them.  So each time you restart the gateway, run:

     `$ sudo chown -R apache:apache /var/run/ceph/`

######If using CivetWeb (Hammer)
Civetweb debugging info goes into the radosgw logs with the preface 'civetweb:'. To increase the log level civetweb, uncomment the line `debug civetweb=10` in `/etc/ceph/conf` and restart ceph-radosgw.


[top](#top)

***

##<a name="useful_links"></a>Useful Links

* [Installation/config guide from Red Hat](https://access.redhat.com/documentation/en/red-hat-ceph-storage/version-1.2.3/red-hat-ceph-storage-123-ceph-object-gateway-for-rhel-x86-64/ceph-object-gateway-for-rhel-x86-64)
* [Ceph Docs - Firefly Object Gateway](http://docs.ceph.com/docs/v0.80.5/install/install-ceph-gateway/)
* [Ceph Docs - Hammer Object Gateway](http://docs.ceph.com/docs/master/install/install-ceph-gateway/)
* Useful pages from Ceph's bug tracker:
     * [mod_proxy_fcgi](http://tracker.ceph.com/issues/10551)
     * [civetweb] (http://tracker.ceph.com/issues/7933)
* [[Talking to the RADOS Gateway]]

[top](#top)
