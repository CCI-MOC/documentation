Currently we are using foreman, foreman-proxy, and quickstack puppet manifests to pxe boot and puppet provision our openstack nodes in the North-Eastern cluster.

We first created a HIL project, network, and headnode.

```
# create project
haas project_create moc-prod
# create network
haas network_create moc-provision moc-prod moc-prod ""
# create headnode
haas headnode_create prod-foreman moc-prod centos_base
haas headnode_create_hnic prod-foreman eth0
haas headnode_connect_network prod-foreman eth0 moc-provision
# attach nodes to project / network
haas project_connect_node moc-prod cisco-46
haas node_connect_network cisco-46 enp130s0f0 moc-provision vlan/native
haas project_connect_node moc-prod cisco-47
haas node_connect_network cisco-47 enp130s0f0 moc-provision vlan/native
```
Resulting haas configuration:

* haas project: moc-prod
* haas network: moc-provision - vlan 1004
* haas headnode: prod-foreman
* haas allocated nodes: 46, 47

Pre-install set-up on the centos_base vm to get it ready for foreman:
* Set fqdn: chose foreman.moc.neu.edu
* Ensure interfaces are up correctly:
  - had to create ifcfg-[interface] scripts in /etc/sysconfig/network-scripts to bring up both ifaces in consistent manner
  - eth1 is on libvirt network, has address 192.168.122.162
  - ens7 is a bridge on top of vnic, was created by haas... brought it up with IP 10.13.37.1

Next, we installed foreman on the prod-foreman vm:
```
rpm -ivh http://yum.puppetlabs.com/puppetlabs-release-el-7.noarch.rpm
yum -y install epel-release http://yum.theforeman.org/releases/1.8/el7/x86_64/foreman-release.rpm
yum -y install foreman-installer
foreman-installer --foreman-proxy-tftp=true
foreman-installer --foreman-proxy-dns=true --foreman-proxy-dns-interface=ens7 --foreman-proxy-dns-zone=moc.ne.edu --foreman-proxy-dns-forwarders=10.13.37.1
foreman-installer --foreman-proxy-dhcp=true --foreman-proxy-dhcp-interface=ens7 --foreman-proxy-dhcp-gateway=10.13.37.1 --foreman-proxy-dhcp-range="10.13.37.2 10.13.37.200" --foreman-proxy-dhcp-nameservers="10.13.37.1"
```

We had to turn selinux to permissive mode to be able to serve tftp files from the foreman machine.


[OUTDATED, CURRENTLY REMOVED] Finally, we installed the foreman-discovery plugin:
```
foreman-installer --enable-foreman-plugin-discovery
```

Configuration instructions here:
* http://theforeman.org/plugins/foreman_discovery/2.0/#3.Configuration

We can't uninstall foreman-discovery... foreman grrrrrrrrr... we're working around it, fixed the pxe boot menu (pxeboot global default) under hosts-> provisioning templates to have the right IP for the foreman host, so the discovery image will work.


[https://github.com/CCI-MOC/moc/wiki/foreman---issues-faced]

Foreman IPtable rules:
[root@foreman ~]# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:http
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:pcsync-https
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:https

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination         

We need to open up http and https ports so that they are accessible from HaaS master.

Commands:

iptables -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT
iptables -A INPUT -p tcp -m tcp --dport 8443 -j ACCEPT

iptables-save | sudo tee /etc/sysconfig/iptables
service iptables restart