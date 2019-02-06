## Start
### Installing Puppet on centos 6.4

(For illustrative purposes, we started with two kickstart-clean boxes: 8, 10.)

Add the puppet labs, RDO, and EPEL repos on master and client, using the following commands.

    rpm -ivh http://yum.puppetlabs.com/el/6/products/i386/puppetlabs-release-6-6.noarch.rpm
    yum install -y http://rdo.fedorapeople.org/openstack/openstack-grizzly/rdo-release-grizzly.rpm
    rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm

Install and run the Puppet Master, rake, and git. Run the Puppet Master, and configure it to run on startup:

    yum install -y puppet-server rake git
    /etc/init.d/puppetmaster start
    puppet resource service puppetmaster ensure=running enable=true

Install puppet on the client:

    yum install -y puppet

Run the following iptables command on the SERVER to ensure that the port is open for signing certs. This command is moronically excessive, but it is the only thing which we have found to work consistently.  It also prevents an annoying MySQL error during installation.

    iptables -I INPUT -m tcp -p tcp -j ACCEPT

In `/etc/hosts` on both machines add `127.0.1.1` as their respective hostnames. On the client also add the IP and hostname of the master.  For example, on client moc-node-9 with server moc-node-11, under domain name `moc.bu.edu`, add

    127.0.1.1    moc-node-9.moc.bu.edu
    192.168.3.9  moc-node-9.moc.bu.edu
    192.168.3.11 moc-node-11.moc.bu.edu

Add to `/etc/puppet/puppet.conf` (on BOTH client and master) the following lines to the agent section.  (For example, in our setup, `puppet-master-hostname` is `moc-node-9.moc.bu.edu`.)

    server=<puppet-master-hostname>
    report=true
    pluginsync=true

Make sure to use FQDNs in `/etc/hosts` and `/etc/puppet/puppet.conf`.  Furthermore, make sure that your DHCP server or static network configuration sets both the domain name and the domain name to search correctly.

On the client, run

    puppet agent --test --waitforcert 20

to send a cert to the master and wait 20 seconds to pull a response.  Then on the master, run

    puppet cert list

to find the cert needing a signature, and sign it with

    puppet cert sign <hostname of client>

The puppet master-client connection is now complete, and we're ready for installation of OpenStack.


### Installing Openstack

Start by installing the `puppetlabs/openstack` module on the master.

    puppet module install puppetlabs/openstack

Put your site manifest file at `/etc/puppet/manifests/site.pp` .  Ours is a highly edited version of `/etc/puppet/modules/openstack/tests/site.pp`, which is in the repo as `site.pp`.  Finally, deploy the install by running the following on BOTH machines.

    puppet agent -t


### Errors we've encountered

  - Running `puppet agent -t` on the master or client
    - Error: Failed to apply catalog: getaddrinfo: Name or service not known
      - Had to edit master's `puppet.conf` to include who the master is. This is fixed above
    - Lots of cert errors: specifically master not seeing / responding to client cert request, but generate says that the client has an unsigned cert waiting
      - This is just SSL problems, the solution is just destroy and remake all offending files
      - Try to sign all certs. Clear all certs. Remove `/var/lib/puppet/ssl/` on both master and client. Restart puppetmaster service. Should work from there.
  - Running `puppet agent -t --certname moc-node-8"`
    - Error: Could not retrieve catalog from remote server: Error 400 on SERVER: Invalid parameter export_resources at `/etc/puppet/manifests/site.pp:86` on node moc-node-8
      - Apparently `export_resources` isn't a valid option? Current fix is to simply remove it
      - Should look more into
    - Error: Could not retrieve catalog from remote server: Error 400 on SERVER: Must pass `secret_key` to `Class[Openstack::Controller]` at `/etc/puppet/manifests/site.pp:86` on node moc-node-8
      - The default site.pp doesn't include passing `secret_key` in the controller, the included `site.pp` has this fixed
      - Should look more into
    - Error: Failed to apply catalog: Parameter name failed on Database_user[root@]: Invalid database user root@ at /etc/puppet/modules/mysql/manifests/server/account_security.pp:13
      - We have a perhaps partial workaround, which is to eliminate the two lines near the beginning of the file that contain `{::fqdn}`.  George suspects that this may be because we are not setting fqdn anywhere, and that perhaps we should be.
      - This is now fully solved, and integrated into the text.
  - After finishing the install, Horizon is broken.  This is due to SELinux preventing httpd from communicating with keystone.  Brute-force solution: `setenforce 0`.  Only do this if you want Adam to kill you.
  - If you get an error, that nova/rabbitmq.pp is looking for rabbitmq::server and it doesn't exist, it's because you're using too recent of a release of the puppetlabs/rabbitmq module.  v2.1.0 works and v3.0.0-rc1 and rc2 both do NOT work.

### Things that are currently mis-configured

  - The floating IP range is incorrect.
     - It should be able to be a range of IPs in a larger subnet:  ideally something like 192.168.3.170-172.  The problem:  I don't know how to make it not be a CIDR, and I don't understand how it's being set.  The given CIDR eventually ends up in `/etc/nova/nova.conf` as the option `floating_range`.  But this option does not exist, according to the Grizzly documentation at http://docs.openstack.org/grizzly/openstack-compute/admin/content/list-of-compute-config-options.html .  Interestingly, the corresponding `fixed_range` is deprecated, and I'm not sure what's supposed to replace its functionality yet.
     - It turns out that this is no longer managed in `nova.conf`.  Instead, floating IP addresses can be added to a pool on the command line.  Therefore, our site.pp no longer tries to create any floating IP addresses.
     - It is indeed possible to reach VMs, with these floating IP addresses.
  - Instance VNC consoles are broken:  error code 1006 while connecting.

### Future areas of improvement in configuration

  - All machines should log to a single logging node, with syslog.
  - Quantum/Neutron, Cinder, and Swift should be enabled.
  - Nodes should be monitored with something like Nagios.