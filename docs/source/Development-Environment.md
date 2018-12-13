# Development Environment
*31 July 2015: Most (if not all) of the information on this page is deprecated.  It is being archived for reference.*

For our development environment, we use vagrant to create the base virtual machines with libvirt, then use a modified version of the puppetlabs-openstack git repo to deploy Openstack on top of the virtual machines.

### To launch a new virtualized Openstack environment

Install prerequisites
```
# vagrant - go to https://www.vagrantup.com/downloads and choose the correct package for you platform
# apt-get install kvm libvirt-bin git
# adduser $USER libvirtd
```

Obtain the MOC puppetlabs-openstack code
```
# Git pull https://github.com/CCI-MOC/puppetlabs-openstack.git
```

Navigate to the subdirectory `puppetlabs-openstack/examples/libvirt`

Run `install_requirements.sh`

Run `firstrun.sh`

The authentication credentials are on the control node, in `/root/openrc`

### To rebuild

Run `50_destroy_nodes.sh`

Run `firstrun.sh`

### To add an additional service to be automatically installed using puppet

Add a new entry to the Puppetfile at the top level of the puppetlabs-openstack directory

Add the correct pointer to the puppet module you added to the role for the machine you want in in, specified under `puppetlabs-openstack/manifests/roles`

Versions we are using for tools and plugins
* Vagrant 1.6.3
* Vagrant-hostmanager 1.5.0
* Vagrant-libvirt 0.0.16
* Puppet 3.6.2

### Current shortcomings
Only one development environment can exist on a machine.

The private IP addresses are hard-coded into the vagrant file, so duplicate vm's networks would conflict.

### Issues I encountered
Vagrant-libvirt does not install the most recent version by default, and needs to be specified in the install. 

This is in `install-requirements.txt`.

If it is not the most recent version, removing domains will not work correctly, and you will have to manually remove virtual machines.
 
