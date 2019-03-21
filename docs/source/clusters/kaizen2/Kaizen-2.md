## Kaizen Networks
This documents lists out all hosts on all Kaizen VLANs.

### Public Neu (VLAN 127)
 -  192.12.185.247      kzn-e.massopen.cloud emergency connection is through BU public net
 -  Subnet: 129.10.5.0/24

```shell
IP Address  Hostname/Description
129.10.5.4	kzn-hil-client.infra.massopen.cloud
129.10.5.32	node-32 (old kaizen controller-1)
129.10.5.39	node-39 (old kaizen services box)
129.10.5.47	node-47 (old kaizen controller-2)
129.10.5.48	kzn-h.infra.massopen.cloud (node-48)
129.10.5.50	Virtual IP for openstack horizon (kaizen.massopen.cloud)
129.10.5.52	Repo server

*Stack 2*
129.10.5.1	Router IP (on cisco switch) (Highly Available)
129.10.5.2	Router IP (on cisco switch)
129.10.5.3	Router IP (on cisco switch)
129.10.5.40	dns server for openshift subdomains (openapp.cloud, mocapps.cloud, massopen.cloud test subdomains)
129.10.5.41	dns server for massopen.cloud

129.10.5.100	Horizon/API HA VIP k.massopen.cloud, new stack, to become kaizen.massopen.cloud

129.10.5.101	ov1.massopen.cloud
129.10.5.102	ov2.massopen.cloud
129.10.5.103	ov3.massopen.cloud 192.12.185.247 *129.10.5.103* is local only
129.10.5.104	ovirt.massopen.cloud (Web interface)
129.10.5.105	kzn-ipmi-gw.infra.massopen.cloud
129.10.5.106	kzn-rtr.infra.massopen.cloud (for all subnets)
129.10.5.107	kzn-ssh.infra.massopen.cloud
129.10.5.108	mochat.infra.massopen.cloud (shared repo server)
129.10.5.109	kzn-nagios.infra.massopen.cloud
129.10.5.111	control1.massopen.cloud
129.10.5.112	control2.massopen.cloud
129.10.5.113	control3.massopen.cloud
129.10.5.114	wl.massopen.cloud
129.10.5.115	tld.massopen.cloud
129.10.5.116	freeipa.infra.massopen.cloud
129.10.5.117	osticket.massopen.cloud
129.10.5.118	sso2.massopen.cloud
129.10.5.119	dns.massopen.cloud
129.10.5.120	helpdeskvm.massopen.cloud
129.10.5.121	massopen.cloud
129.10.5.122	DVBackup
129.10.5.123	reports.infra.massopen.cloud
129.10.5.124	kzn-ipmi2-gw.infra.massopen.cloud
129.10.5.125	kaizen.massopen.cloud - info page and redirect to kaizenold
129.10.5.126	kumo-e.massopen.cloud
129.10.5.127    kzn-hil.massopen.cloud
129.10.5.128    kzn-ovpn.massopen.cloud
129.10.5.129    e1-emergency.massopen.cloud
129.10.5.130    neu-15-41 (for apoorve)
129.10.5.131    zabbix.massopen.cloud
```

## IPMI network: 10.0.0.0/19 (ACCESS MODE)
 -  IP assignment: 10.0.rack-number.u(nit)-number, servers bigger than 1 U get the lowest U number,
 numbering increases from bottom to top.
 -  10.0.0.X (X froom 1 to 255, 255 is valid because prefix is 19) (for VMs that can be anywhere)
```shell
IP Address  Hostname/Description
10.0.0.1    kzn-ipmi-gw.infra.massopen.cloud
10.0.0.2    kzn-cacti
10.0.0.3    kzn-hil-server.infra.massopen.cloud
10.0.0.4    kzn-nagios.infra.massopen.cloud
10.0.0.5    ov3.massopen.cloud/kzn-e.massopen.cloud over BU public IPs from Kumo
10.0.0.6    kzn-ipmi2-gw.infra.massopen.cloud ***ON 1GB VLAN 2 TAGGED ***
10.0.0.255  kzn-h.infra.massopen.cloud
```
## Ceph client (VLAN 250)
 -  Subnet: 192.168.0.0/19
```shell
IP Address  Hostname/Description
Range for Dirctor 192.168.0.0 - 192.168.3.255
192.168.28.1    kzn-osd01
192.168.28.2    kzn-osd02
192.168.28.3    kzn-osd03
192.168.28.4    kzn-osd04
192.168.28.5    kzn-osd05
192.168.28.6    kzn-osd06
192.168.28.7    kzn-osd07
192.168.28.8    kzn-osd08
192.168.28.198  kzn-cacti
192.168.28.199  RGW2
192.168.28.200  RGW1
192.168.28.201  kzn-mon1
192.168.28.202  kzn-mon2
192.168.28.203  kzn-mon3

?? nagios interface

192.168.28.205  neu-3-30-bmi2
192.168.28.211  kzn-vbmi01res.kzn.moc
192.168.28.212  kzn-vbmi01stack.kzn.moc
192.168.28.213  kzn-vbmi02res.kzn.moc
192.168.28.214  kzn-vbmi02stack.kzn.moc
```

### Provision/SNMP network
 -  172.16.0.0/19, Undercloud network
 -  192.168.255.0/24 both on (VLAN 201)
```shell
IP Address  Hostname/Description
172.16.0.1	 kzn-router.infra.massopen.cloud
172.16.0.3	 kzn-foreman.infra.massopen.cloud, kzn-foreman.moc.int
172.16.0.4       kzn-nagios.infra.massopen.cloud
172.16.0.5       kzn-undercloud.infra.massopen.cloud
172.16.0.6       kzn-ipmi-gw.infra.massopen.cloud
172.16.0.7       kzn-cacti
172.16.0.8       kzn-dns2
172.16.0.201-255 Foreman DHCP range
```
IP assignment - same as IPMI network. BMI has to configure additional tagged interface probably DHCP provided by Foreman

See [rack numbers and details](https://docs.google.com/spreadsheets/d/1kKNx2HKtuTL_UZFDsOMfKqk-9KJ3Yd46lvJRn6P-3Zc/edit#gid=0)

### Intranet: 172.16.96.0/19 (VLAN 204)
```shell
IP Address  Hostname/Description
172.16.96.1	kzn-router.infra.massopen.cloud
172.16.96.2     kzn-hil-server.infra.massopen.cloud
172.16.96.3     kzn-hil-client.infra.massopen.cloud
172.16.96.4     kzn-vbmi01res.infra.massopen.cloud
172.16.96.5     kzn-cacti
172.16.96.6     kzn-nagios
```
 -  Defauly gateway at 172.16.96.1 connects to internet. Will be registered in HIL so nodes can access internet.

### Staging Foreman: 172.17.0.0/19 (VLAN 269)
```shell
IP Address  Hostname/Description
172.17.0.1    	kzn-router.infra.massopen.cloud
172.17.0.3      Foreman
172.17.0.5      Undercloud VM for staging
172.17.0.5      kzn-ipmi-gw.infra.massopen.cloud

```


### BMI Provisioning (VLAN 500)
 -  dhcp-range=10.255.0.45,10.255.0.254,7d
```shell
IP Address  Hostname/Description
10.255.0.1       kzn-vbmi01res
10.255.0.2       kzn-vbmi02res
10.255.0.3       kzn-nagios.infra.massopen.cloud
10.255.0.4       kzn-ipmi-gw
10.255.0.5       kzn-rtr
10.255.0.35      kzn-rmon1
10.255.0.39      kzn-rmon2
10.255.0.41      kzn-rmon3
10.255.0.255	 kzn-cacti
```

### Stack BMI provision network: 10.254.0.0/19 (VLAN 501)
```shell
IP Address  Hostname/Description
10.254.0.1       kzn-vbmi01stack
10.254.0.2       kzn-vbmi02stack
10.254.0.3       kzn-nagios.infra.massopen.cloud
10.254.0.255	 kzn-cacti
```

### CSAIL-3803 Public IPs: 128.31.28.0/24 (VLAN 3803)
```shell
IP Address  Hostname/Description
128.31.28.1     gateway
128.31.28.248   openshift (Rob)
128.31.28.249	openshift (Rob)
128.31.28.251   openshift (Rob)
128.31.28.252   openshift (Rob)
128.31.28.253   openshift (Rob)
128.31.28.254   openshift (Rob)
```

### Research Ceph (VLAN 252)
```shell
IP Address  Hostname/Description, HIL managed vlan 252
192.168.32.1 kzn-rmon1 kzn-rmon1.infra.massopen.cloud
192.168.32.2 kzn-rmon2 kzn-rmon2.infra.massopen.cloud
192.168.32.3 kzn-rmon3 kzn-rmon3.infra.massopen.cloud
192.168.32.4 kzn-ssh.infra.massopen.cloud
192.168.32.5 kzn-rtr.infra.massopen.cloud
192.168.32.6 kzn-zabbix

192.168.35.21 neu-3-21 neu-3-21.infra.massopen.cloud
192.168.35.22 neu-3-22 neu-3-22.infra.massopen.cloud
192.168.35.23 neu-3-23 neu-3-23.infra.massopen.cloud

192.168.37.21 neu-5-21 neu-5-21.infra.massopen.cloud
192.168.37.22 neu-5-22 neu-5-22.infra.massopen.cloud
192.168.37.23 neu-5-23 neu-5-23.infra.massopen.cloud

192.168.47.21 neu-15-21 neu-15-21.infra.massopen.cloud
192.168.47.22 neu-15-22 neu-15-22.infra.massopen.cloud
192.168.47.23 neu-15-23 neu-15-23.infra.massopen.cloud
```
 -  HIL/BMI users need to have access to ceph-public network (HIL admin operation),
 add it to their project/nodes/nics and configure an ip, ip is calculated as described below (user operations)
 -  For client IPs use 192.168.32+Cnum.Unum mask 255.255.224.0,
 where Cnum is cage number and Unum is U number. Cage and U numbers are used in node mame - neu-1-21 means Cage 1, U 21 and the ip would be 192.168.33.21
 -  Ssh access is by using FreeIpa accounts, nodes are accessible from BMI provision
 subnet (500) by name. Ssh to monitor nodes to get ceph.conf and admin keyring.
 -  Please use ecrbd pool for rbd images to save disk space. To create an image using erasure
 coded pool for data storage use `rbd create mynewrbdimage --data-pool ecrbd --size xxxG`
 -  *Be careful with the number of placement groups if creating new pools*

### VPN range
172.31.224.0/19

### Network connections
For 1 GB U number goes to the same port number.

For 10GB U1 goes to ports 1 and 2 on lower Brocade switch, U2 - ports 3,4 up to U20.
U21 goes to ports 1,2 on upper switch and so on.

### Lenovo Ceph - for IBM Power9 (VLAN 210 and 211)

VLAN 210 - 	192.168.96.0/19
VLAN 211 - 	192.168.128.0/19

