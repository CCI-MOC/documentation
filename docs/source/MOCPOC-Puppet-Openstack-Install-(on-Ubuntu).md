This wiki page documents the process of setting up OpenStack using Puppet Lab's OpenStack module. It primarily contains notes related to the specific hardware set up of the MOCPOC test cloud and is only a supplement to the original documentation (found [here](https://github.com/stackforge/puppet-openstack)). 

## Contents
* [Start](#start)
* [Single-Node OpenStack](#single-node)
* [Multi-Node Openstack](#multi-node)

<a name="start"></a>
## Start

### Installing Puppet on Ubuntu
The standard Ubuntu Precise repositories are too old, so enable the Puppetlabs repo, and then install Puppet.

    wget http://apt.puppetlabs.com/puppetlabs-release-precise.deb
    dpkg -i puppetlabs-release-precise.deb
    apt-get update
    apt-get install puppet

### Openstack Module
Install the most recent release (1.1.0) from the forge:

    puppet module install puppetlabs-openstack

<a name="single-node"></a>
## Single-Node OpenStack (a test install) 
Used: A single desktop machine running Ubuntu 12.04 with two nics.

### Network Configuration
This configuration uses nova-network's [Flat DHCP](http://docs.openstack.org/trunk/openstack-compute/admin/content/configuring-flat-dhcp-networking.html) in multi-host mode (soon to be deprecated in favor of Quantum).
* Public Network: Set to eth1, connected to the outside world
* Private Network: Set to eth0, a nic connected to nothing. Nova-Network expects a private nic it can configure and bridge VM to. 

### Block Storage
If you do not set cinder, puppet will try to configure nova-volume. To use nova-volume, you must create a volume group named cinder-volumes, otherwise create nova-volumes.

#### Volume Group
Write a file the size of the LVM volume you want to create, associate with loop device, and make a partition table with a single, primary partition with system id 8e (for LVM):

    $ dd if=/dev/zero of=cinder-volumes bs=1 count=0 seek=2G
    $ losetup /dev/loop2 cinder-volumes
    $ fdisk /dev/loop2
    #Type in the followings:
    $ n
    $ p
    $ 1
    $ ENTER
    $ ENTER
    $ t
    $ 8e
    $ w

After this formatting, you might have to run `partprobe` to update the kernel's partition table. Then initialize the disk to be used by LVM, 

    $ pvcreate /dev/loop2
    $ vgcreate cinder-volumes /dev/loop2

After creating and attaching cinder storage, configure (with mkfs) and mount. 

### RabbitMQ
Do not set rabbit user (correctly defaults to nova user)
This breaks authentication with rabbit -- I don't know why

### Run!
To begin the cascade of puppet manifests that configure your hardware with all components of Openstack, you simple need to set a collection of variables in one puppet file.  To configure OpenStack exactly as describe above, use the all-in-one.pp manifest in the [puppet-manifest](https://github.com/CCI-MOC/moc-public/tree/master/puppet-manifests) folder of this git repository.

Note:
* Original example file can be found in directory: puppet/modules/openstack/examples/site.pp
* The first class called is a module that can be found here: puppet/modules/openstack/manifests/all.pp

Now, to begin OpenStack:

    $ puppet apply all-in-one.pp --certname openstack_all

The `--certname` flag specifies the configuration of the `openstack_all` node. This file includes the configuration of only one type of node, where as the original site.pp has includes classes to configure a control node and compute node separately.  You do not have to include the `--certname` flag if the hostname of the machine is the same as `openstack_all`.

<a name="multi-node"></a>
## Multi-Node Openstack (with Puppet Master)

Again, working through the official tutorial [here](https://github.com/stackforge/puppet-openstack).

Notes/Todos:

* set ephemeral storage to use local disk (root file system is NFS for all compute nodes). Use `instance path` flag in nova.conf ([nova conf documentation](http://docs.openstack.org/folsom/openstack-compute/admin/content/list-of-compute-config-options.html))

* You can use the nova_config type outside of the openstack modules to set any additional nova.conf parameter. For example:

    nova_config { 'instances_path': value => '/path/to/instances' }

* Each OpenStack component has its own *_config type. Grep the manifests in the openstack module to see more examples.

### Physical Configuration
* Puppet Master will run on Deathstar in the moc-nova VM (see: [[Networking Configuration of MOC POC]])
* Puppet Slave is in the PXE booted cluster, using Temple with ip address 192.168.3.11, nat'ed through Deathstar to outside world

### Installing puppet master and connecting to client
* Follow the  Installing Puppet instructions in this wiki
* Follow the Install Puppet instructions in https://github.com/stackforge/puppet-openstack 
* When editing /etc/puppet/puppet.conf on the client, change <CONTROLLER_HOSTNAME> to moc-puppet-master.bu.edu or whatever else the puppet master is called
* Ensure that /etc/hosts on the client includes 127.0.1.1 moc-puppet-client and a line with the ip and alias for moc-puppet-master.bu.edu and moc-puppet-master:

    127.0.1.1       moc-puppet-client
    192.168.3.74	moc-puppet-master.bu.edu	moc-puppet-master

* Similarly ensure that /etc/hosts on the master includes

    127.0.1.1      moc-puppet-master

### Steps done
* configured cinder-volumes volume group on moc-nova
* configured moc-nova as puppet master
  * apt-get install rake git puppetmaster
* DID NOT enable to mess with [storeconfigs](http://projects.puppetlabs.com/projects/1/wiki
/Using_Stored_Configuration), needed for openstack puppet master, according to docs
* installed puppet on moc-nova
* installed puppet on Temple






<a name="multi-node-vms"></a>
## Multi-Node Openstack,on VMs (with Puppet Master)
NO LONGER BEING WORKED ON

steps: 

1. set up networking, two bridges.
    * br1: public,  bridged to eth1 and master's tap1 interface
    * br0: private, bridged only to master's tap0 interface and slave's tap2 interfaces

Other notes:
 sudo apt-get install bridge-utils uml-utilities qemu-kvm

2. set up tap interfaces and add to bridge? can we do this in /etc/network/interfaces?

3. create disk for VMS

4. create vms
