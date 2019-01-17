# SaltStack

### Key Terms
**Salt** 
* a configuration management system, capable of maintaining remote nodes in defined states (for example, ensuring that specific packages are installed and specific services are running)
* a distributed remote execution system used to execute commands and query data on remote nodes, either individually or by arbitrary selection criteria

It's an opensource, fast flexible and scalable solution built on python that can run configuration in parallel on a massive scale with simplicity.

**Salt Cloud**

A suite of tools used to create and deploy systems on many hosted cloud providers.

**Master**

A central Salt daemon which from which commands can be issued to listening minions.

**Minion**

A server running a Salt minion daemon which can listen to commands from a master and perform the requested tasks. Generally, minions are servers which are to be controlled using Salt.

**State**

A data structure which contains a unique ID and describes one or more states of a system such as ensuring that a package is installed or a user is defined.  

**Pillar**

A simple key-value store for user-defined data to be made available to a minion. Often used to store and distribute sensitive data to minion.  

**Grain**

A key-value pair which contains a fact about a system, such as its hostname, network addresses.
    
[Installation](Salt.html#installation)   
[Configuration](Salt.html#configuration)   
[State Execution/Node Configuration](Salt.html#execution-of-sls-files)   
[Salt-Cloud](Salt.html#salt-cloud)   
[Pillars](Salt.html#pillars)  

### Installation:
Run the following commands to install the SaltStack repository and key:

	sudo yum install http://repo.saltstack.com/yum/redhat/salt-repo-latest-2.el7.noarch.rpm

If there is an error that no repository is enabled then add below yum repository configuration in `/etc/yum.conf` to install the required base packages
```
 [saltstack-repo]
 name=SaltStack repo for Red Hat Enterprise Linux $releasever
 baseurl=https://repo.saltstack.com/yum/redhat/$releasever/$basearch/latest
 enabled=1
 gpgcheck=1
 gpgkey=https://repo.saltstack.com/yum/redhat/$releasever/$basearch/latest/SALTSTACK-GPG-KEY.pub
 https://repo.saltstack.com/yum/redhat/$releasever/$basearch/latest/base/RPM-GPG-KEY-CentOS-7
```   

*May need to install systemd and python-systemd*

	sudo yum clean expire-cache
	sudo yum install salt-master	     # to install master
	sudo yum install salt-minion	     # to install minion
	
### Configuration:
Modify `/etc/salt/minion` on the minion to specify the master for that minion.

	master: <ip or hostname of the master>

To run the configuration using `.sls` files, modify the file `/etc/salt/master`

```
# uncomment the below lines under the heading "file server settings" and create/check the location "/srv/salt"
file_roots:
    base:
     - /srv/salt
```

Start the salt master

	sudo salt-master -l info -d		(-l log level, -d daemon process)

Start the salt minion
   
	sudo salt-minion -l info -d		(-l log level, -d daemon process)

Salt uses AES encryption for all communication between master and minion.

In order to communicate with the master, minion will try to connect with it. However, it will wait for its request to be accepted by the master and the keys being exchanged among them.
   
Use below commands on the master to list, accept, reject (if unaccepted) or delete (if accepted) the keys from the minions
```
salt-key -L                                  # list all the key status
	
salt-key -A                                  # accept all the keys
salt-key -a <keyname/hostname>               # accept a specific minion
	
salt-key -r <keyname>                        # reject an unaccepted key
salt-key -R                                  # reject all unaccepted keys

salt-key -d <keyname>                        # delete an accepted key
salt-key -D                                  # delete all accepted keys
```

Once the key exchange is done, the minions are ready to accept commands from the master.
	
Verify the communication among master and minions, run the command on master to receive an acknowledgement from the minions.

	salt '*' test.ping

### Execution of sls files:
`.sls` files are used to store the state declarations written in YAML to configure the minions by running it on matser.  

The minions can be configured either running specific `.sls` files or running the hierarchy of config files defined using a `top.sls` file. `top.sls` contains the target and the file system locations to be checked for `init.sls` file for execution.  

Add `top.sls` file with below content in the salt file system ie. `/srv/salt`
```
base:
  '*':
    - conf
    - conf.sconf
```   

Create the paths `/srv/salt/conf` and `/srv/salt/conf/sconf` as specified in `top.sls`

Add `init.sls` within the above locations along with other supporting resources required for configuration `/etc/conf/init.sls`
```
python-pip:
  pkg:
    - installed

test.txt:
  file.managed:
    - source: salt://conf/test.txt
    - name: /home/centos/test_cp.txt
    - user: centos
    - mode: 700
```

This file installs python-pip and copies a `test.txt` file to the specified location on minions. `test.txt` is present in /etc/conf`
   
In `/etc/conf/sconf/init.sls`
   
```
gcc:
  pkg.installed
```
   
This file installs gcc package.
   
Run all the configuration files specified in `top.sls` as below  

	sudo salt '*' state.highstate
   
To run a specific config file

```
sudo salt '*' state.sls testConfig         # testConfig is a .sls file at /src/salt
   
sudo salt '*' state.sls conf.init          # to run a specific init file without changing other files
sudo salt '*' state.sls conf
   
sudo salt '*' state.sls conf,conf.sconf    # run multiple specific files. conf.sconf specifies the location conf/sconf
```
	
To check the grains on the system

```
sudo salt '*' grains.item os               # check specifc item info in grain eg. os
   
sudo salt '*' grains.items                 # display all the items in the grains
```

### Salt-cloud
Install salt-cloud by running the first two steps in the installation of salt master/minion installation.
   
	sudo yum install salt-cloud

*Note:  Make sure salt-master is installed and running so that when salt-cloud provisions a VM, it could be federated to the cluster. Also, it requires python-openstack client to be installed in order to communicate with OpenStack.*
   
Generate a cloud provider config file `openstack.conf` at the location `/etc/salt/cloud.providers.d`
   
```
my-openstack-config:
  # Set the location of the salt-master
  minion:
    master: <master ip or hostname>
    
  # Configure the OpenStack driver
  identity_url: https://keystone.kaizen.massopencloud.org:5000/v2.0/tokens
  compute_name: nova
  protocol: ipv4

  compute_region: MOC_Kaizen

  # Configure Openstack authentication credentials
  user: <openstack dashboard user>
  password: <OpenStack dashboard pass>
    
  # tenant is the project name
  tenant: <project name>

  driver: openstack

  # skip SSL certificate validation (default false)
  insecure: false
```

Generate a cloud profile config file `openstack.profile.conf` at `/etc/salt/cloud.profiles.d`

This can have multiple profiles with different profileID ie. first key of the group `openstack_512` within the same file    
   
```
openstack_512:                                  # profileID
  provider: my-openstack-config                 # provider ID
  size: m1.small                                # vm flavor
  image: centos-6.7                             # image name
  ssh_key_file: <ssh .pem key file location>    
  ssh_key_name: <ssh key pair name>
  ssh_interface: private_ips                    
  ssh_username: centos
```

Generate the cloud map config file `openstack.map.conf` at `/etc/salt/cloud.maps.d`

Multiple VMs could be created with various profiles suing the same map file.
   
```
openstack_512:                  # profileID
  {% for i in range(1,4)%}
  - vm{{i}}:                    # vm name
    minion:                     # minion config
      master: 192.168.100.170   
  {%endfor%}
```
   
For VM provisioning without using jinja template

```
openstack_512:
   - vm1:
       minion:
         master: 192.168.100.170
   - vm2:
       minion:
         master: 192.168.100.170		
```
	
Execute the config files to create the VM on openstack, configure them as salt minion and fedrate them to the salt cluster.

	sudo salt-cloud -m openstack.map.conf -l info

This will connect to the OpenStack using provider config, create 3 VMs name vm1, vm2 and vm3 as per map config with a profile in profile config file.
   
VMs can be created directly using the profile config file as below  

	sudo salt-cloud -p openstack.profile.conf vm6,vm7

### Pillars
These are used to store requirement specific parameters/static data as key value pairs.

Go to `/etc/salt/master` and update/uncomment the pillar file system location under 'Pillar settings':

```
pillar_roots:
  base:
    - /srv/pillar
```

Create a `top.sls` file under the above pillar path `/srv/pillar`:

```
base:
  '*':
    - params
```

Create an init file under the params with the key value pairs as below:

```
#key:value
pkg:salt

#key:list of values
pkgs:
  - salt
  - slurm
  - sshfs
```

Access a key value pair in the salt files as below:

```
{{ pillar['pkg'] }}:
  pkg.installed:
```

Access a list

```
packages:
  pkg.installed:
    {% for pkg in pillar.get('pkgs',{})%}
    - {{ pkg }}
    {% endfor %}
```

*Note: If a change is made to the pillar file, then it should be reloaded/refreshed in all the nodes*

```
salt '*' saltutil.refresh_pillar
```

