Here we describe how the default vlan head nodes are configured

# virtual machine setup

The Vm is given 2 nics, one attached to our public network (eth1), and
one attached to the vlan for which it is responsible (eth0).

We also add a filesystem device, mode should be 'mapped', source should be
`/var/lib/headnode-config/${vlan_number}`, we're using `/etc/moc` as a target
(note that this is just a tag; it doesn't place any constraints on where the vm
actually mounts the filesystem), and we make the filesystem readonly

Note that the directory in question *must* exist, or the vm will not start,
giving an error message along the lines of 'Connection reset by peer'.

Also, (on ubuntu) the apparmor profile for libvirt needs to be adjusted to provide read
access to this directory. We did this by simply adding:

    /var/lib/headnode-config/** r,

To the end of `/etc/apparmor.d/abstractions/libvirt-qemu`.

# OS

We use ubuntu 12.04. In addition to the base install, we need to add the
following packages:

 - isc-dhcp-server
 - tftpd-hpa
 - python-pkg-resources
 - python-requests
 - syslinux-common
 - git
 - fcgiwrap

We only want the dhcp server listening on eth0, so modify
`/etc/default/isc-dhcp-server` to contain:

    INTERFACES="eth0"

copy these files from `/usr/lib/syslinux` to `/var/lib/tftpboot`:

 - `pxelinux.0`
 - `menu.c32`
 - `memdisk`
 - `mboot.c32`
 - `chain.c32`

/etc/dhcp/dhcpd.conf looks like:

    default-lease-time 36000;
    max-lease-time 36000;
    allow booting;
    allow bootp;
    authoritative;

    subnet 192.168.10.1 netmask 255.255.255.0 {
        range 192.168.10.0 192.168.10.127;
        option broadcast-address 192.168.10.255;
        filename "/pxelinux.0";
    }

We need to make sure our network is set up correctly. `/etc/network/interfaces` might look like:

    # The loopback network interface
    auto lo
    iface lo inet loopback
    
    # Our ip address on the private network, shared with the workstations.
    auto eth0
    iface eth0 inet static
        address 192.168.10.254
        netmask 255.255.255.0
    
    # This affords us an ip address on the workstations' management subnet.
    # We need this for e.g. power cycling.
    auto eth0:1
    iface eth0:1 inet static
        address 10.0.0.254
        netmask 255.255.255.0
    
    # The public network interface
    auto eth1
    iface eth1 inet dhcp

start the dhcp server:

    service isc-dhcp-server start

To actually boot something, you'll need to create a config file for
pxelinux under `/var/lib/tftpboot/pxelinux.cfg/`. The following will boot from the local disk:

    default disk
    label disk
        LOCALBOOT 0

We use this as `/var/lib/tftpboot/pxelinux.cfg/default`, which is the config file for machines that don't have a more specific config provided.
    
You can mount the virtio filesystem we set up via (e.g.):

    mount -t 9p -o ro,trans=virtio /etc/moc /etc/moc

We have the following in /etc/fstab:

    /etc/moc /etc/moc 9p noauto,ro,trans=virtio 0 0

The mount type, '9p', is a Plan 9 network protocol by which the host filesystem is being exported.  The `noauto` option prevents the filesystem from being mounted during the boot process. This is a workaround for an as yet unexplained issue where the filesystem will fail to mount during boot, but mounts fine once the machine is up. we put:

    mount /etc/moc

At the beginning of `/etc/rc.local` to achieve the same effect.

This directory contains a file `machines.txt`, which looks like:

    <mac-address> <management-ip-address>
    <mac-address> <management-ip-address>
    ...

with one line per slave node. The mac address corresponds to the interface on the private network, and the ip address is the AMT management BIOS's management ip.

To get the various scripts we need onto the headnode, we can do:

    git clone git://github.com/CCI-MOC/moc-public
    cd moc-public/python-mocutils
    python setup.py build && sudo python setup.py install

This includes things like power_cycle, linky.py, etc.

## HTTP Server

We have an http server running on our head nodes as well, for two reasons:

1. We want to use kickstart to do centos installs, and it can't grab the ks.cfg file from tftp
2. We need a way for the installer to notify the head node that the installation is complete, so that the next time the machine boots, pxelinux will chainload to the disk, rather than start another install.

To this end, we configure our server as follows:

 - our document root is `/var/lib/tftpboot`.
 - We have a cgi script ready to handle notifications from the installer.

We're currently using nginx as our web server, though there isn't a particular reason for that. The bulk of our configuration is in `/etc/nginx/sites-enabled/tftp`, which looks like:


    server {
        # We only want to listen on the private interface; the public internet
        # doesn't need access.
        listen 192.168.10.254:80;
    
        root /var/lib/tftpboot;
        index index.html index.htm;
    
        # Make site accessible from http://localhost/
        server_name localhost;
    
        location / {
            # First attempt to serve request as file, then
            # as directory, then fall back to index.html
            try_files $uri $uri/ /index.html;
        }
    
        # This allows the kickstart post-install script to inform us that it
        # should boot to the disk going forward, rather than starting the
        # installer.
        location /boot-default/ {
            fastcgi_pass  unix:/var/run/fcgiwrap.socket;
            fastcgi_param SCRIPT_FILENAME /usr/local/bin/boot-default.py;
            fastcgi_param QUERY_STRING $query_string;
            fastcgi_param REQUEST_METHOD $request_method;
            fastcgi_param CONTENT_TYPE $content_type;
            fastcgi_param CONTENT_LENGTH $content_length;
        }
    
    }

For the second block to work, We need fcgiwrap installed and running.

The script itself is installed with the rest of python-mocutils. for it to
work, the webserver user needs write access to
`/var/lib/tftpboot/pxelinux.cfg`.

# Kickstarting CentOS

In order to do a PXE based install of centos, we created a directory
`/var/lib/tftpboot/centos`, with the following contents:

 - our [ks.cfg][1]. This sets up CentOS, puppetizes it, and adds the repos we
   need for openstack (and puppet, of course).
 - The centos kernel and initrd. These can be obtained from the CentOS 6.4
   install cd - they are the files `vmlinuz` and `initrd.img` in the
   `isolinux` directory.
 - A file `pxelinux.cfg`, which looks like:

    default rhel
    label rhel
        kernel centos/vmlinuz
        append initrd=centos/initrd.img quiet ksdevice=eth0 ks=http://192.168.10.254/centos/ks.cfg

The `ksdevice` parameter makes sure the machine grabs `ks.cfg` via the private
network. It may not actually be necessary to specify this.

To actually start an install we need to make symlinks to this file in
`/var/lib/tftpboot/pxelinux.cfg/`. As an example, if we want to kickstart a
machine who's mac address is `aa:bb:cc:dd:ee:ff`, We could do:

    cd /var/lib/tftpboot/pxelinux.cfg
    ln -s ../centos/pxelinux.cfg 01-aa-bb-cc-dd-ee-ff

(In general, replace the colons in the mac with dashes, and prepend an `01-`).
Then, We can power-cycle the machine, and it will do an install, then reboot.
The last line in `ks.cfg` invokes the cgi script mentioned above, which will
delete our symlink. We want machines not doing an install to boot from disk,
so `/var/lib/tftpboot/pxelinux.cfg/default` should look like:

    default disk
    label disk
        LOCALBOOT 0

The script [linky.py][2] will automate creating links for all of the machines
listed in `/etc/moc/machines.txt`, and then power cycling them to start the
install.

# iptables setup

We have a number of services running on the headnode, and while most of them
are configured to only listen on the private interface, it would be prudent to
ensure that we're not accepting unwanted traffic from the outside. to that
end, we configure iptables to drop everything coming from outside, except ssh
traffic.

# Puppet

We have a puppetmaster set up, as described at [[MOCPOC: Puppet Openstack Install (on Ubuntu)]] and 
[[MOCPOC: Puppet Openstack Install (on Centos)]]

# TODO:

[1]: /CCI-MOC/moc-public/blob/master/ks.cfg
[2]: /CCI-MOC/moc-public/python-mocutils/scripts/linky.py
