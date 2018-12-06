kvm how-to (_complete_ work in progress, primarily notes)
useful reference: http://gmplib.org/~tege/qemu.html


1. Set up public and private networks for VMS to bridge to (Ian's file net.sh). Public and private nics must be set to promiscuous. 

_Questions:_ Do we need to install bridge-utils?

auto br0
iface br0 inet dhcp
    hwaddress ether 00:06:5B:3C:44:BF
    bridge_ports    eth0
    bridge_stp      off
    bridge_maxwait  0
    bridge_fd       0

 # Set up interfaces manually, avoiding conflicts with, e.g., network manager
 iface eth0 inet manual

 iface eth1 inet manual

 # Bridge setup
 iface br1 inet dhcp
        hwaddress ether 00:06:5B:3C:44:BF
        bridge_ports eth1 

 iface br2 inet static
        hwaddress ether 00:06:5B:3C:44:BE

2. 
    $ apt-get install qemu-kvm
3. create vm insterfaces, owned by user, to hand to vms
    $ sudo tunctl -u <user> -t tap<n>

Todo: 
* edit /etc/network/interfaces for public and private bridges
* script that spins up VMs also initializes the tap interfaces

# qemu-img summary

important options:

 * -f : file format (qcow2, vdi, raw, etc...)
 * -b : base image (if making a copy on write image)

create a 20 Gigabyte disk image in qcow2 format:

    qemu-img create -f qcow2 filename.qcow2 20G

in general:

    qemu-img create -f <file-foramt> <filename> <size>

create a qcow2 image with orig.qcow2 as a base (will be the same size):

    qemu-img create -f qcow2 -b orig.qcow2 filename.qcow2

# qemu-system-x86_64

Important options:

  * -hda <file> : use <file> as hard disk 0 image

Example, to netboot a kvm instance. and redirect serial to stdio:

    qemu-system-x86_64 -enable-kvm -m 512 -net nic,vlan=0 -net tap,vlan=0,ifname=tap0,script=no,downscript=no -serial stdio

Example, to boot from iso and start a vnc server on display 1:

    qemu-system-x86_64\
    	-enable-kvm\
    	-hda single.qcow2\
    	-m 4096\
    	-net nic,vlan=0\
    	-net tap,vlan=0,ifname=tap0,script=no,downscript=no\
        -cdrom <path/to/iso>\
        -display vnc 1
