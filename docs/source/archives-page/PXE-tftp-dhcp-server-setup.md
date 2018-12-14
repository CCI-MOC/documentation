# PXE boot server setup

Just an outline of what was done to set up the pxe boot server. Still a work in progress.

Loosely based on <https://help.ubuntu.com/community/PXEInstallMultiDistro>
and <http://www.techienote.com/2010/11/pxe-boot-server-on-ubuntu.html>

- installed ubuntu 12.04 on the machine (anakin in our case).
- configure the network with static ip.
- install packages:
  - tftpd-hpa (tftp server, for serving up boot images)
  - dhcp3-server (dhcp server)
  - syslinux (boot loader)
- edit /etc/dhcp/dhcpd.conf, adding the following lines.

    allow booting;
    allow bootp;
    subnet 10.0.0.0 netmask 255.255.255.0 {
             range 10.0.0.11 10.0.0.200
             filename "/pxelinux.0"
    }

- copy boot files to tftp directory:

    cp /usr/lib/syslinux/pxelinux.0 /var/lib/tftpboot/
    cp /usr/lib/syslinux/menu.c32 /var/lib/tftpboot/
    cp /usr/lib/syslinux/memdisk /var/lib/tftpboot/
    cp /usr/lib/syslinux/mboot.c32 /var/lib/tftpboot/
    cp /usr/lib/syslinux/chain.c32 /var/lib/tftpboot/