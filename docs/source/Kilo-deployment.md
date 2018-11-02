(07/21/2015) ----

Weird note - from machines in cluster, can ping vm with large packet size (worked up to 60,000 bytes). From my machine, can only ping up to 1472 bytes, meaning 1480 byte ping packets with the 8 byte header...

(07/20/2015) ----

DHCP issue on production cluster solved by creating a new network. Packets were still showing on br-int on the *controller* using the first network, but the dhcp port was down. We've *never* been able to see traffic on br-tun on either the controller or the compute node.

We still need to fix ssh from outside of the cluster to a virtual machine floating IP. 

There are a couple other things we want as well:
* ceph integration
  * puppetizing pushing ceph.conf and ceph keys to nodes
* turn on ssl in puppet manifests
* turn on optional services?

Neutron Debugging on BIG DATA environment - Ravi (07/19/2015):

Me/Yue started on Kilo Deployment on nodes 30,31 and 32. We were able to deploy Controller and Compute nodes successfully. We used puppet scripts that Jon used for his deployment. We are able to spawn a VM and get to outside world and ping this VM from external world.

Issues:
-Not able to login using ssh. It seems that it could be related to below point.
-But the download and upload speeds are extremely slow.  The MTU issue that we discussed seemed to promising.


Following are the list of things that I tried from Friday but not with much luck:

Did a comparison with Harvard cluster - Noticed following changes:

-N/W name in puppet script pointing to physext1. So changed it and created external network agin- Didn't work.

Other Changes made:
-DNSmasq(Even though this is GRE related and we are using VXLAN for tunneling)
-l2 population enabled in controller

Interesting thing to be noted here is we are able to login from contoller.

Next Steps:

-Need to take tcpdumps on routers and their configurations.
-Need to take tcpdumps on physical NICS on the controller and compute nodes as well as bridges created through OVS.


###Neutron networking setup issues:
- [2015July16] Debug session with Jonathan Proulx https://etherpad.csail.mit.edu/p/moc-neutron1
>
What we've got:

    enp130s0f0     -- tagged vlans and native mode for VLAN?? 10.13.37.4/24

    enp130s0f0.125 -- vlan 125 public IPv4 (129.10.3.47/25 currently VIA   br-ex)

>
    [root@node-47 ~(openstack_admin)]# neutron net-show public_network
    +---------------------------+--------------------------------------+
    | Field                            | Value                                          |
    +---------------------------+--------------------------------------+
    | admin_state_up                  | True                                 |
    | id                                             | 586c7161-e6c0-427e-9b39-b42303feca39 |
    | mtu                                         | 0                                    |
    | name                                      | public_network                       |
    | provider:network_type         | local                                |
    | provider:physical_network |                                      |
    | provider:segmentation_id  |                                      |
    | router:external                       | True                                 |
    | shared                                    | True                                 |
    | status                                     | ACTIVE                               |
    | subnets                                 | 5a877a85-b57e-485d-94a0-e3e054897a57 |
    | tenant_id                               | 6d8e903c98ce4bad8030836293a63868     |
    +---------------------------+--------------------------------------+

    What looks wierd to me (jproulx):

    - provider:physical_network should be 'physext1' because:

               [root@node-47 ~(openstack_admin)]# grep br-ex /etc/neutron/plugins/openvswitch/ovs_neutron_plugin.ini
           bridge_mappings =physext1:br-ex

    - Provider:network_type should be 'flat' (local means loopback only) http://docs.openstack.org/admin-guide-cloud/content/provider_terminology.html

    MTU of 0 is suspicious, but this field is new with Kilo so '0' may be special or may just be wrong IDK

    Looking at https://github.com/CCI-MOC/moc/wiki/Northeastern-Cluster it seems VLAN250 is what's supposed ot be used for 192.168.1.0/24

    If that is the case enp130s0f0.250 should be on br-ex and not enp130s0f0.125 which should bs a 'normal' linux interface with the 129.10.3.47/25 address.

###Problems faced with puppet running on machines, after kickstart install:
1. Error: Could not find puppet master
  * solution: added IP entry to /etc/hosts for 10.13.37.1 foreman.moc.ne.edu

2. Error: Could not retrieve facts: no address found for node xxxxxxx
  * solution: added IP entry to /etc/hosts for node

3. Error: Could not find openstack::keystone class
  * solution: added https://github.com/stackforge/puppet-openstack module

4. Error: Bad params from openstack::keystone to ::keystone
  * solution: changing passthrough variables in openstack::keystone

5. Error: NetworkManager messing up public interface
  * solution: systemctl stop NetworkManager

### Problems faced when puppetizing:
1. Error: Port, address, and protocol are deprecated params for all endpoints
  * Solution: added fully qualified url to params -> controller_common -> openstack::keystone -> bottom level auth manifests
2. Error: more depricated parameters, just names switching
  * Solution: fixed the names in the manifests. Lots of stuff like "connection" -> "db_connection"...
3. Error: no neutron set up created
  * Solution: controller_common only does some neutron endpoint config. To set up the services, you need to use quickstack::neutron::controller as your base manifest.
4. Error: no ovs set-up on controller
  * Solution: added the old config of ovs from moc_config::controller.pp from our icehouse deployment.

### Problems faced with running openstack
1. Error: Keystone tokens time out at random intervals
  * Solution: run ntpdate when box is connected to public network, so time can sync correctly

###Useful links
- Bridge/OVS trouble shooting https://bugs.launchpad.net/openstack-manuals/+bug/1379391
- OpenStack Network Troubleshoopting http://docs.openstack.org/openstack-ops/content/network_troubleshooting.html

###Configuration being used:
quickstack top level manifest, found here: 
* https://github.com/redhat-openstack/astapor/tree/master/puppet/modules/quickstack/manifests
openstack modules from RHEL OSP 7.0 openstack-puppet rpm

* RHEL OSP 7 source code (Beta) can be found here:
ftp://partners.redhat.com/9be6fa88fb85ba9c78c8f3fe47e038c5/OpenStack/
