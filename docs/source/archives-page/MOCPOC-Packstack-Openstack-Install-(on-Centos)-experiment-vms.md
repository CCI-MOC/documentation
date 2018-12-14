### Controller on VM, Compute on PXE booted ramdisk hardware (from now on, these machines will be referred to as the BORG). (VAL+ANDREW)

### Did
Created a VM with public and private nic to be the controller node. 

    $ sudo yum install -y http://rdo.fedorapeople.org/openstack/openstack-grizzly/rdo-release-grizzly-3.noarch.rpm
    $ sudo yum install -y openstack-packstack

Ran `packstack --allinone`, rebooted, edited the answer file create by `--allinone` 

* set `CONFIG_CINDER_INSTALL` and `CONFIG_SWIFT_INSTALL` to `n`
* `CONFIG_NOVA_COMPUTE_HOSTS=192.168.3.3` (the ip address of one member of the BORG)
* and, appropriately, `CONFIG_NOVA_COMPUTE_PRIVIF=eth0`
* we also set `CONFIG_NOVA_NETWORK_PRIVIF=eth1` and `CONFIG_NOVA_NETWORK_PUBIF=eth2`, although these a nova-network variables.
* we left all quantum configurations as is:
  * `CONFIG_QUANTUM_SERVER_HOST=128.197.44.248` The IP addresses of the server on which to install the Quantum server 
  * `CONFIG_QUANTUM_USE_NAMESPACES=y` Enable network namespaces for Quantum 
  * `CONFIG_QUANTUM_L3_HOSTS=128.197.44.248` A comma separated list of IP addresses on which to install Quantum L3 agent 
  * `CONFIG_QUANTUM_L3_EXT_BRIDGE=br-ex` The name of the bridge that the Quantum L3 agent will use for external traffic 
  * `CONFIG_QUANTUM_DHCP_HOSTS=128.197.44.248` A comma separated list of IP addresses on which to install Quantum DHCP plugin 
  * `CONFIG_QUANTUM_L2_PLUGIN=openvswitch` The name of the L2 plugin to be used with Quantum 
  * `CONFIG_QUANTUM_METADATA_HOSTS=128.197.44.248` A comma separated list of IP addresses on which to install Quantum metadata agent 
  * `CONFIG_QUANTUM_LB_TENANT_NETWORK_TYPE=local` The type of network to allocate for tenant networks 
  * `CONFIG_QUANTUM_LB_VLAN_RANGES=` A comma separated list of VLAN ranges for the Quantum linuxbridge plugin 
  * `CONFIG_QUANTUM_LB_INTERFACE_MAPPINGS=` A comma separated list of interface mappings for the Quantum linuxbridge plugin 
  * `CONFIG_QUANTUM_OVS_TENANT_NETWORK_TYPE=local` Type of network to allocate for tenant networks 
  * `CONFIG_QUANTUM_OVS_VLAN_RANGES=` A comma separated list of VLAN ranges for the Quantum openvswitch plugin 
  * `CONFIG_QUANTUM_OVS_BRIDGE_MAPPINGS=` A comma separated list of bridge mappings for the Quantum openvswitch plugin 

When running packstack with the edit answer file, and we ran into!

### Problems!

1. Horizon did not work immediately after making new project. Continues to have error "unable to retrieve quota information". This is fixed by logging out and logging back in.

2. First notable problem: packstack stalled in state `Testing if puppet apply is finished : 192.168.3.3_quantum.pp` for >12 hours.

3. after `crtl-c`-ing the install, we got the message _Kernel package with netns support has been installed on host 192.168.3.89. Please note that with this action you are loosing Red Hat support for this host. Because of the kernel update host mentioned above requires reboot._
 * BUT! we cannot reboot the BORG machines 

4. the BORG machine wrote _Message from syslogd@moc-compute-03 at Jun  6 13:15:28 ...
 kernel:journal commit I/O error_ to stdout at the time of the second packstack run.

5. We noticed the time is wrong on our compute machine
 * __FIXED__: reboot with ntp on of the BORG
 * this particular solution had not effect on the part (1) or part (2) above.

### Now trying, packstack w/out Quantum!
How to:

1. get repos and yum install openstack-packstack (here: http://openstack.redhat.com/Quickstart)
2. run `packstack --gen-answer-file=~/answers.cfg`
3. edit answer file. See the ans.txt file save in the github (all passwords set to "GEN")"[[https://github.com/CCI-MOC/moc-public/blob/master/packstack/ans.txt]]

#### Problems:

When using a BORG machine, we had to manually start libvirt and messagebus and openstack-nova compute. We then get this error printed to stdout: _Message from syslogd@moc-compute-03 at Jun  6 13:15:28 ...kernel:journal commit I/O error_ 

Problem with squash nfs?

### Now trying physical machine for compute, instead of BORG!

After restating libvirt, messagebus and openstack-nova-compute, can boot machines!

##### Notes on NOT using quantum:

* Default nova network settings: FlatDHCP server with multi_host=false (the default setting of multi_host -- variable not set in /etc/nova/nova.conf). THIS IS PERFECT, as out BORG nodes only have 1 NIC





