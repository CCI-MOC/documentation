# Kaizen Networks
This documents lists out all hosts on all Kaizen VLANs.

## Public Neu (VLAN 127)

-  Subnet: 129.10.5.0/24
```
IP Address       Hostname/Description
129.10.5.1       Router IP (on cisco switch) (Highly Available)
129.10.5.2       Router IP (on cisco switch)
129.10.5.3       Router IP (on cisco switch)
129.10.5.4       kzn-hil-client.infra.massopen.cloud

129.10.5.40      dns server for openshift subdomains (openapp.cloud, mocapps.cloud, massopen.cloud test subdomains)
129.10.5.41      dns server for massopen.cloud

129.10.5.55      dhcp1.cnv.massopen.cloud
129.10.5.56      dhcp2.cnv.massopen.cloud

129.10.5.101     ov1.massopen.cloud
129.10.5.102     ov2.massopen.cloud
129.10.5.103     ov3.massopen.cloud 192.12.185.247 *129.10.5.103* is local only
129.10.5.104     ovirt.massopen.cloud (Web interface)
129.10.5.105     kzn-ipmi-gw.infra.massopen.cloud
129.10.5.106     kzn-rtr.infra.massopen.cloud (for all subnets)
129.10.5.107     kzn-ssh.infra.massopen.cloud
129.10.5.108     mochat.infra.massopen.cloud (shared repo server)
129.10.5.109     kzn-nagios.infra.massopen.cloud
129.10.5.110     rhel-6-repomirror.massopen.cloud

129.10.5.114     wl.massopen.cloud (to be deleted)
129.10.5.115     tld.massopen.cloud
129.10.5.116     freeipa.infra.massopen.cloud
129.10.5.117     netbox.massopen.cloud
129.10.5.118     techsquare.massopen.cloud
129.10.5.119     dns.massopen.cloud
129.10.5.120     helpdeskvm.massopen.cloud (to be deleted)
129.10.5.121     massopen.cloud
129.10.5.123     reports.infra.massopen.cloud (to be deleted)
129.10.5.124     kzn-ipmi2-gw.infra.massopen.cloud

129.10.5.127     kzn-hil.massopen.cloud (to be deleted)
129.10.5.128     kzn-ovpn.massopen.cloud

129.10.5.132     zabbix.massopen.cloud

129.10.5.133     rz.massopen.cloud (Research Zabbix, to be deleted)
129.10.5.134     mondata.massopen.cloud (to be deleted)

129.10.5.136     elk.massopen.cloud (powered off, so ping won't respond)
129.10.5.137     promtest.infra.massopen.cloud (to be deleted)

129.10.5.139     kumo-hil-client.infra.massopen.cloud (because BU network is down).

129.10.5.140     esi-undercloud.massopen.cloud
129.10.5.141     esi-controller-0.massopen.cloud
129.10.5.142     esi-controller-1.massopen.cloud
129.10.5.143     esi-controller-2.massopen.cloud
129.10.5.144     esi-vip

129.10.5.146     sso1.massopen.cloud (name resolves to CSAIL address)
129.10.5.147     sso2.massopen.cloud (name resolves to CSAIL address)
129.10.5.148    sso.massopen.cloud (VIP)

129.10.5.149     new-esi-undercloud.massopen.cloud
129.10.5.150     new-esi-controller-0.massopen.cloud
129.10.5.151     new-esi-controller-1.massopen.cloud
129.10.5.152     new-esi-controller-2.massopen.cloud
129.10.5.153     new-esi-vip
```

## Public BU (VLAN 105)

-  Subnet: 192.12.185.0/24

```
IP Address       Hostname/Description
192.12.185.1     default gateway


192.12.185.254   ov3.massopen.cloud (though this name resolves to 129.10.5.103)
```

## IPMI network: 10.0.0.0/19 (VLAN 1 on 1G network, untagged)

 -  IP assignment: 10.0.rack-number.u(nit)-number, servers bigger than 1 U get the lowest U number,
 numbering increases from bottom to top.
 -  10.0.0.X (X froom 1 to 255, 255 is valid because prefix is 19) (for VMs that can be anywhere)

```
IP Address       Hostname/Description
10.0.0.1         kzn-ipmi-gw.infra.massopen.cloud
10.0.0.2         kzn-cacti
10.0.0.3         kzn-hil-server.infra.massopen.cloud
10.0.0.4         kzn-nagios.infra.massopen.cloud
10.0.0.5         zabbix.massopen.cloud
10.0.0.6         kzn-ipmi2-gw.infra.massopen.cloud ***ON 1GB VLAN 2 TAGGED ***
10.0.0.7         switchback.massopen.cloud (The host that backs up switch configurations).
10.0.0.8         promtest.massopen.cloud (Test promotheus instance. Talk to Lars/Naved about it).
10.0.0.255       kzn-h.infra.massopen.cloud
10.0.3.140       ov3.massopen.cloud/kzn-e.massopen.cloud over BU public IPs from Kumo
10.0.5.140       ov2.massopen.cloud
10.0.15.140      ov1.massopen.cloud
```

## IPMI network (VLAN 911, for accessing OCT/Umass nodes): 10.2.0.0/19

There's dnsmasq on `kzn-ipmi-gw.infra.massopen.cloud` to serve IPs to UMass nodes' BMCs.

```
IP Address       Hostname/Description
10.2.0.1         kzn-ipmi-gw.infra.massopen.cloud

10.2.0.3         maas.massopen.cloud
10.2.0.4         kzn-zabbix

10.2.0.41        INTEL-1
10.2.0.43        INTEL-2
10.2.0.45        INTEL-3

10.2.0.50        Dynamic Range Start
10.2.0.100       Dynamic Range Stop

10.2.4.0        OCT4-00
10.2.4.1        OCT4-01
10.2.4.2        OCT4-02
10.2.4.3        OCT4-03
10.2.4.4        OCT4-04
10.2.4.5        OCT4-05
10.2.4.6        OCT4-06
10.2.4.7        OCT4-07
10.2.4.8        OCT4-08
10.2.4.9        OCT4-09
10.2.4.10       OCT4-10
10.2.4.11       OCT4-11 (NODE BROKEN)
10.2.4.12       OCT4-12
10.2.4.13       OCT4-13
10.2.4.14       OCT4-14
10.2.4.15       OCT4-15
10.2.4.16       OCT4-16
10.2.4.17       OCT4-17
```

## IPMI network (VLAN 912, for accessing OCT/Umass nodes for operatre first): 10.3.0.0/19

There's dnsmasq on `kzn-ipmi-gw.infra.massopen.cloud` to serve IPs to UMass nodes' BMCs.

```
IP Address       Hostname/Description
10.3.0.1         kzn-ipmi-gw.infra.massopen.cloud

10.3.0.3         maas.massopen.cloud

10.3.0.50        Dynamic Range Start
10.3.0.100       Dynamic Range Stop

10.3.3.0         OCT3-00
10.3.3.1         OCT3-01
10.3.3.2         OCT3-02
10.3.3.3         OCT3-03
10.3.3.4         OCT3-04
10.3.3.5         OCT3-05
10.3.3.6         OCT3-06
10.3.3.7         OCT3-07
10.3.3.8         OCT3-08
10.3.3.9         OCT3-09
10.3.3.10        OCT3-10
10.3.3.11        OCT3-11
10.3.3.12        OCT3-12
10.3.3.13        OCT3-13
10.3.3.14        OCT3-14
10.3.3.15        OCT3-15
10.3.3.16        OCT3-16
10.3.3.17        OCT3-17
10.3.3.18        OCT3-18 (NODE BROKEN)
10.3.3.19        OCT3-19
10.3.3.20        OCT3-20
10.3.3.21        OCT3-21
10.3.3.22        OCT3-22
10.3.3.23        OCT3-23
10.3.3.24        OCT3-24
10.3.3.25        OCT3-25
10.3.3.26        OCT3-26
10.3.3.27        OCT3-27
10.3.3.28        OCT3-28
10.3.3.29        OCT3-29
10.3.3.30        OCT3-30 (NODE BROKEN)
10.3.3.31        OCT3-31

10.3.10.0        OCT10-00
10.3.10.1        OCT10-01
10.3.10.2        OCT10-02
10.3.10.3        OCT10-03
10.3.10.4        OCT10-04
10.3.10.5        OCT10-05
10.3.10.6        OCT10-06
```

## New England Storage Exchange (NESE) (VLAN 211): 10.0.120.0/22

Note: In order to connect to NESE, add the following routes.
```
- to: 10.255.116.0/23
  via: 10.0.120.1
- to: 10.247.236.0/25
  via: 10.0.120.1
- to: 140.247.236.0/25
  via: 10.0.120.1
```

```
IP Address       Hostname/Description
10.0.120.1       NESE gateway
```

## Production Ceph Cluster iSCSI (VLAN 213): 10.21.0.0/22

```
IP Address  Hostname/Description
10.21.3.1   neu-5-30-iscsi1
10.21.3.2   neu-3-30-vm-iscsi2
```

## Ceph cluster/replication (VLAN 248)

 -  Subnet: 192.168.255.0/24

```
IP Address       Hostname/Description
192.168.255.1    ov1
192.168.255.2    ov2
192.168.255.3    ov3
192.168.255.250  nfs-ha virtual ip
192.168.255.251  nfs1
192.168.255.252  nfs2
```
## Ceph cluster/replication (VLAN 249)

-  Subnet: 192.168.40.0/23

```
IP Address  Hostname/Description
TBD
```
## Ceph client (VLAN 250)

-  Subnet: 192.168.0.0/19

```
IP Address       Hostname/Description
#Range for Director 192.168.0.0 - 192.168.3.255
192.168.28.1     kzn-osd01
192.168.28.2     kzn-osd02
192.168.28.3     kzn-osd03
192.168.28.4     kzn-osd04
192.168.28.5     kzn-osd05
192.168.28.6     kzn-osd06
192.168.28.7     kzn-osd07
192.168.28.8     kzn-osd08
192.168.28.198   kzn-cacti
192.168.28.199   RGW2
192.168.28.200   RGW1
192.168.28.201   kzn-mon1
192.168.28.202   kzn-mon2
192.168.28.203   kzn-mon3

?? nagios interface

192.168.28.205   neu-3-30-bmi2
192.168.28.211   kzn-vbmi01res.kzn.moc
192.168.28.212   kzn-vbmi01stack.kzn.moc
192.168.28.213   kzn-vbmi02res.kzn.moc
192.168.28.214   kzn-vbmi02stack.kzn.moc
```

## Provision/SNMP network

-  172.16.0.0/19, Undercloud network
-  192.168.255.0/24 both on (VLAN 201)

```
IP Address       Hostname/Description
172.16.0.1       kzn-router.infra.massopen.cloud
172.16.0.3       kzn-foreman.infra.massopen.cloud, kzn-foreman.moc.int
172.16.0.4       kzn-nagios.infra.massopen.cloud
172.16.0.5       kzn-undercloud.infra.massopen.cloud
172.16.0.6       kzn-ipmi-gw.infra.massopen.cloud
172.16.0.7       kzn-cacti
172.16.0.8       kzn-dns2
172.16.0.201     Start foreman DHCP range
172.16.0.255     End Foreman DHCP range
```
IP assignment - same as IPMI network. BMI has to configure additional tagged interface probably DHCP provided by Foreman

See [rack numbers and details](https://docs.google.com/spreadsheets/d/1kKNx2HKtuTL_UZFDsOMfKqk-9KJ3Yd46lvJRn6P-3Zc/edit#gid=0)

## Intranet: 172.16.96.0/19 (VLAN 204)

```
IP Address       Hostname/Description
172.16.96.1      kzn-router.infra.massopen.cloud
172.16.96.2      kzn-hil-server.infra.massopen.cloud
172.16.96.3      kzn-hil-client.infra.massopen.cloud
172.16.96.4      kzn-vbmi01res.infra.massopen.cloud
172.16.96.5      kzn-cacti
172.16.96.6      kzn-nagios
```
 -  Defauly gateway at 172.16.96.1 connects to internet. Will be registered in HIL so nodes can access internet.


## BMI Provisioning (VLAN 500)

-  dhcp-range=10.255.0.45,10.255.0.254,7d

```
IP Address       Hostname/Description
10.255.0.1       kzn-vbmi01res
10.255.0.2       kzn-vbmi02res
10.255.0.3       kzn-nagios.infra.massopen.cloud
10.255.0.4       kzn-ipmi-gw
10.255.0.5       kzn-rtr
10.255.0.35      kzn-rmon1
10.255.0.39      kzn-rmon2
10.255.0.41      kzn-rmon3
10.255.0.255     kzn-cacti
```

## Stack BMI provision network: 10.254.0.0/19 (VLAN 501)

```
IP Address       Hostname/Description
10.254.0.1       kzn-vbmi01stack
10.254.0.2       kzn-vbmi02stack
10.254.0.3       kzn-nagios.infra.massopen.cloud
10.254.0.255     kzn-cacti
```

## CSAIL-3801 Public IPs; 128.31.20.0/22 (VLAN 3801)

Statically assigned IPs on this network are:

```
IP Address       Hostname/Description
128.31.20.1      gateway

128.31.20.2      Start: MOC Infrastructure reserved
128.31.20.20     End: MOC Infrastructure reserved

128.31.20.21     Start: ESI floating IP
128.31.20.255    End: ESI floating IP
```

## Research Ceph (VLAN 252)

```
IP Address       Hostname/Description
192.168.32.1     kzn-rmon1 kzn-rmon1.infra.massopen.cloud
192.168.32.2     kzn-rmon2 kzn-rmon2.infra.massopen.cloud
192.168.32.3     kzn-rmon3 kzn-rmon3.infra.massopen.cloud
192.168.32.5     kzn-rtr.infra.massopen.cloud
192.168.32.6     kzn-zabbix

```

## VPN range

172.31.224.0/19

## Network connections

For 1 GB U number goes to the same port number.

For 10GB U1 goes to ports 1 and 2 on lower Brocade switch, U2 - ports 3,4 up to U20.
U21 goes to ports 1,2 on upper switch and so on.

## Lenovo Ceph - for IBM Power9 (VLAN 210 and 211)

VLAN 210 -  192.168.96.0/19
VLAN 211 -  192.168.128.0/19
