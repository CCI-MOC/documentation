Here, we outline the process we used for getting ubuntu 12.04 to PXE boot, with its root filesystem on an nfs share. This article focuses primarily on client setup; a functioning dhcp/tftp/nfs server is assumed. To get started quickly, we used [Bccd](http://bccd.net).

# Overview

We'll assume you have an existing ubuntu workstation to work with. It doesn't have to be 12.04 for this to work; we used 12.10 during development. We'll be doing the following:

1. Install ubuntu 12.04 in a chroot with debootstrap
2. Modify the chroot as necessary
3. Copy the contents of the chroot to the nfs server
4. Copy the kernel image and initrd to the tftp directory
5. Configure pxelinux to boot the new kernel and ramdisk, with apropriate parameters.

# Creating a chroot with debootstrap

Make sure debootstrap is installed:

    $ sudo apt-get install debootstrap

We'll be using `/moc` as the home of our chroot. Start by creating the directory:

    $ sudo mkdir /moc

The basic usage of debootstrap is:

    debootstrap --arch <architecture> <release> <directory> [ <mirror> ]

In our case, we'll run:

    $ sudo debootstrap --arch amd64 precise /moc http://archive.ubuntu.com/ubuntu

This should install the base system. from here, we'll need to do some configuration.

First, we modify `/etc/fstab` within the chroot to look something like this:

    proc	/proc	proc	defaults	0	0
    sys         /sys	sysfs 	defaults 	0	0
    /dev/nfs	/	nfs	defaults	1	1
    none	/tmp	tmpfs		defaults	0	0
    none	/var/run	tmpfs	defaults	0	0
    none	/var/lock	tmpfs	defaults	0	0
    none	/var/tmp	tmpfs	defaults	0	0

Now we need to do a few things from within the chroot. chroot in and mount /proc:

    $ sudo chroot /moc
    # mount /proc

You'll want to set the root password:

    # passwd

We also need to compile a new kernel; the stock ubuntu kernel is missing a few things, including support for PXE booting and NFS as a root filesystem. Starting from the generic kernel, you'll need to enable the following additional options (each of which should be built into the kernel, not as a module):

 - Networking Options -> Networking Support -> IP: Kernel level autoconfiguration (and the subordinate options (bootp, rarp, dhcp))
 - File Systems -> Network File Systems -> NFS Client Support
   - It probably makes sense to enable both version 3 and 4.
   - Also want "Root file system on NFS"
      - Note that NFS Client Support must be built-in (not a module) for the root fs option to appear.

TODO: document making a debian package out of this

You'll need to edit /etc/initramfs-tools/initramfs.conf. in particular, ensure that you have

    MODULES=netboot

and

    BOOT=nfs

Then, regenerate the initrd:

    mkinitramfs -o initrd.img <kernel-name>

where `<kernel-name>` is appropriate for the kernel you just built (should match the name of a subdirectory of `/lib/modules`

Now, unmount /proc, tar up the chroot, and copy it to the nfs server. make sure the server's /etc/exports has a line such as:

    /path/to/chroot 192.168.3.0/255.255.255.0(rw,fsid=0,no_subtree_check,sync,no_root_sqaush)

Then, edit `${tftpdir}/pxelinux.cfg/default` where `${tftpdir}` is the root of your tftp server (in the case of bccd, this is `/srv/tftp`.) It should look as follows:

    default ubuntu
    label ubuntu
        kernel vmlinuz
        append ETHERNET=eth0 initrd=initrd.img root=/dev/nfs nfsroot=<nfs-ip-addr>:/path/to/chroot ip=dhcp rw

Also copy your kernel image to `${tftpdir}/vmlinuz` and your new initrd to `${tftpdir}/initrd.img`.

From here, you should be able to power on an appropriately-networked machine and have it boot ubuntu.

# Aufs Root

This section is derived from: <http://debianaddict.com/2012/06/19/diskless-debian-linux-booting-via-dhcppxenfstftp/>. The steps we actually need (which aren't already taken care of) are:

aufs module:

    echo aufs >> /srv/nfsroot/etc/initramfs-tools/modules

Create the file /srv/nfsroot/etc/initramfs-tools/scripts/init-bottom/aufs give it executable permissions and fill it with the following

    modprobe aufs
    mkdir /ro /rw /aufs
    mount -t tmpfs tmpfs /rw -o noatime,mode=0755
    mount --move $rootmnt /ro
    mount -t aufs aufs /aufs -o noatime,dirs=/rw:/ro=ro
    mkdir -p /aufs/rw /aufs/ro
    mount --move /ro /aufs/ro
    mount --move /rw /aufs/rw
    mount --move /aufs /root
    exit 0

Then, regenerate the initrd:

    mkinitramfs -o initrd.img <kernel-name>

and copy it to `${tftpdir}`.