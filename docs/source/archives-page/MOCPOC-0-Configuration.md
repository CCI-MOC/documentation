## Hardware
__Deathstar__: 1 desktop with 32 GB memory, 2 nics, public and private network. Will run the following VMs ([description of VM configuration](Death-Star-VM-configuration.html), [Deathstar IPs](Networking-Configuration-of-MOC-POC.html):

* _moc-dhcp_: PXE server, see [Ubuntu PXE boot image](Ubuntu-PXE-boot-image.html) and [PXE-tftp-dhcp-server-setup](PXE-tftp-dhcp-server-setup.html)
* _moc-nova_: Puppet master and Openstack Control Node (with all services other than nova-compute)
* _moc-nfs_: NFS file server
* _moc-gateway_: gateway for NAT'ed compute nodes

__Compute Nodes__: 6 Optiplex desktops with 4 GB ram each, 2 nics, private network. Will run only Openstack compute. 

* nat-ed to the outside world through Deathstar. 
* Step 1: set up with nova-network. Step 2: set up with quantum networking
* Ramdisk for operating system
  * If openstack is too big, put more on local storage, or ISCSI to file server?
* Openstack Instances ephemeral storage stored on local disk.
* See compute node bios settings: [Optiplex Configuration](../hardware/Optiplex-BIOS-Settings.html)
__


## PXE Server
* BCCD install on KVM instance bridged to the private network. 
* Points to NFS file system.
