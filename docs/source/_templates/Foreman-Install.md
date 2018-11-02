Install base centos 7 OS.

NOTE: Set a dns name for foreman. This will be used in future and can't be changed.

Example: set the hostname to foreman.moc.edu or whatever you want to use in future.

Next, install foreman on the foreman vm:
```
http://theforeman.org/manuals/1.10/index.html#2.Quickstart
rpm -ivh http://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm
yum -y install epel-release http://yum.theforeman.org/releases/1.10/el7/x86_64/foreman-release.rpm
yum -y install foreman-installer
foreman-installer --foreman-proxy-tftp=true
foreman-installer --foreman-proxy-dns=true --foreman-proxy-dns-interface=eth0 --foreman-proxy-dns-zone=moc.mit.edu
foreman-installer --foreman-proxy-dhcp=true --foreman-proxy-dhcp-interface=eth0 --foreman-proxy-dhcp-gateway=10.13.65.1 --foreman-proxy-dhcp-range="10.13.65.2 10.13.65.200" --foreman-proxy-dhcp-nameservers="10.13.65.1"
puppet agent -t
```
If provisioning network is larger than /24 foreman installer will not create reverse DNS zones and creating hosts will fail. Add zone definitions manually in /etc/zones.conf and actual db files in /var/named/dynamic/ - use db.100.168.192.in-addr.arpa as template

We then added the rhel iso (which we got by wget'ing from red hat website) to the webserver with:
```
mount -o loop [iso-name] /usr/share/foreman/public/rhel
```
Had to create a soft link to the hiera config file, and comment out the hiera config line in /etc/puppet/puppet.conf to get puppet and hiera to talk:
```
ln -s /etc/hiera.yaml /etc/puppet/hiera.yaml
## comment out hiera-config location in /etc/puppet/puppet.conf
systemctl restart puppet
```
We installed git, and git cloned kilo-puppet repo to /etc/puppet/environments/production/modules

In the web interface, we configured the host groups moc-controller and moc-compute, added the foreman ip as a custom mirror for the rhel image, and configured the operating system with default rhel pxeboot and rhel kickstart files.

We had to turn selinux to permissive mode to be able to serve tftp files from the foreman machine.

Some issues faced:

https://groups.google.com/forum/#!topic/foreman-users/q-k5twYKNJI

Didn't specify DNS subnet to control, had to fix with:

needed to add needed to add --foreman-proxy-dns-reverse=10.in-addr.arpa, found info here: 
http://projects.theforeman.org/issues/8603


DHCP doesn't work by default in advertising the right Gateway ip-address. Had to modify /etc/dhcp/dhcpd.conf file and add these entries there:-
```
In /etc/dhcp/dhcpd.conf:-
option domain-name-servers 10.14.37.50, 8.8.8.8, 209.244.0.3;

option routers 10.13.37.254;
```
Make sure that the initrd and vmlinuz files are of appropriate size. Else, copy them from the ISO.
```
[root@dev-foreman1 tftpboot]# ls /usr/share/foreman/public/rhel/images/pxeboot/ -lrt
total 74754
-r-xr-xr-x. 2 root root  5026624 Jan 29  2015 vmlinuz
-r--r--r--. 2 root root 36646096 Feb 19  2015 initrd.img
-r--r--r--. 2 root root 34873024 Feb 19  2015 upgrade.img
-r--r--r--. 1 root root      664 Feb 19  2015 TRANS.TBL
[root@dev-foreman1 tftpboot]# cd /usr/share/foreman/public/rhel/images/pxeboot/
[root@dev-foreman1 pxeboot]# cp vmlinuz /var/lib/tftpboot/boot/RedHat-7.1-x86_64-vmlinuz
cp: overwrite ‘/var/lib/tftpboot/boot/RedHat-7.1-x86_64-vmlinuz’? y
[root@dev-foreman1 pxeboot]# cp initrd.img /var/lib/tftpboot/boot/RedHat-7.1-x86_64-initrd.img
cp: overwrite ‘/var/lib/tftpboot/boot/RedHat-7.1-x86_64-initrd.img’? y
[root@dev-foreman1 pxeboot]# cd /var/lib/tftpboot/boot/
[root@dev-foreman1 boot]# ls -lrt
total 40700
-rw-r--r--. 1 foreman-proxy foreman-proxy        0 Mar 31 18:19 CentOS-7.2-x86_64-vmlinuz
-rw-r--r--. 1 foreman-proxy foreman-proxy        0 Mar 31 18:19 CentOS-7.2-x86_64-initrd.img
-rwxr-xr-x. 1 foreman-proxy foreman-proxy  5026624 Apr  2 20:19 RedHat-7.1-x86_64-vmlinuz
-rwxr-xr-x. 1 foreman-proxy foreman-proxy 36646096 Apr  2 20:19 RedHat-7.1-x86_64-initrd.img
[root@dev-foreman1 boot]# 
```
When we did "nslookup compute-25.staging.moc.edu", it was failing on both foreman and the node. We went to domain settings on foreman, modified and set the dns-proxy. We had to then delete and readd the host on foreman. It started working after that.

Updating the cert-requests for running puppet.
```
[root@compute-25 ~]# puppet agent -t
Exiting; no certificate found and waitforcert is disabled

On master node:-
[root@dev-foreman1 hiera]# puppet cert --list
  "compute-25.staging.moc.edu-42c9df2a-33af-4375-a2a5-3625a6b3e3de" (SHA256) CF:5E:7A:B2:CE:5C:E0:4B:63:B2:F7:30:C7:78:4B:48:E5:E4:FE:F3:1D:9D:71:E9:C1:72:61:3C:81:84:03:4A

[root@dev-foreman1 hiera]# puppet cert sign --all
Notice: Signed certificate request for compute-25.staging.moc.edu-42c9df2a-33af-4375-a2a5-3625a6b3e3de
Notice: Removing file Puppet::SSL::CertificateRequest compute-25.staging.moc.edu-42c9df2a-33af-4375-a2a5-3625a6b3e3de at '/var/lib/puppet/ssl/ca/requests/compute-25.staging.moc.edu-42c9df2a-33af-4375-a2a5-3625a6b3e3de.pem'
[root@dev-foreman1 hiera]# 

puppet cert list --all;
```

###### DNS fix
```
# vi /etc/zones.conf <-------- fix the addresses in this file
# cd /var/named/dynamic/
# cp db.100.168.192.in-addr.arpa db.96.13.10.in-addr.arpa
Modify and fix the ip address in copied file
# service named restart
```