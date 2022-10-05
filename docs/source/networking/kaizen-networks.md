# Kaizen Networks
This documents lists out all hosts on all Kaizen VLANs.

## Public Neu (VLAN 127)

-  192.12.185.247      kzn-e.massopen.cloud emergency connection is through BU public net
-  Subnet: 129.10.5.0/24

```
IP Address       Hostname/Description
129.10.5.1       Router IP (on cisco switch) (Highly Available)
129.10.5.2       Router IP (on cisco switch)
129.10.5.3       Router IP (on cisco switch)
129.10.5.4       kzn-hil-client.infra.massopen.cloud

129.10.5.32      node-32 (old kaizen controller-1)

129.10.5.39      node-39 (old kaizen services box)
129.10.5.40      dns server for openshift subdomains (openapp.cloud, mocapps.cloud, massopen.cloud test subdomains)
129.10.5.41      dns server for massopen.cloud

129.10.5.47      node-47 (old kaizen controller-2)
129.10.5.48      kzn-h.infra.massopen.cloud (node-48)

129.10.5.50      Virtual IP for openstack horizon (kaizen.massopen.cloud)

129.10.5.55      dhcp1.cnv.massopen.cloud
129.10.5.56      dhcp2.cnv.massopen.cloud

129.10.5.100     kaizen.massopen.cloud
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
129.10.5.111     control1.massopen.cloud
129.10.5.112     control2.massopen.cloud
129.10.5.113     control3.massopen.cloud
129.10.5.114     wl.massopen.cloud
129.10.5.115     tld.massopen.cloud
129.10.5.116     freeipa.infra.massopen.cloud
129.10.5.117     osticket.massopen.cloud
129.10.5.118     sso2.massopen.cloud
129.10.5.119     dns.massopen.cloud
129.10.5.120     helpdeskvm.massopen.cloud
129.10.5.121     massopen.cloud
129.10.5.122     DVBackup
129.10.5.123     reports.infra.massopen.cloud
129.10.5.124     kzn-ipmi2-gw.infra.massopen.cloud
129.10.5.125     kaizen.massopen.cloud - info page and redirect to kaizenold
129.10.5.126     kumo-e.massopen.cloud
129.10.5.127     kzn-hil.massopen.cloud
129.10.5.128     kzn-ovpn.massopen.cloud
129.10.5.129     e1-emergency.massopen.cloud

129.10.5.132     zabbix.massopen.cloud

129.10.5.133     rz.massopen.cloud (Research Zabbix)
129.10.5.134     mondata.massopen.cloud

129.10.5.136     elk.massopen.cloud (powered off, so ping won't respond)
129.10.5.137     promtest.infra.massopen.cloud
129.10.5.138     p9con.massopen.cloud
129.10.5.139     kumo-hil-client.infra.massopen.cloud (because BU network is down).
129.10.5.140     esi-undercloud.massopen.cloud
129.10.5.141     esi-controller-0.massopen.cloud
129.10.5.142     esi-controller-1.massopen.cloud
129.10.5.143     esi-controller-2.massopen.cloud
129.10.5.144     esi-vip

129.10.5.146     sso1.massopen.cloud (name resolves to CSAIL address)
129.10.5.147     sso2.massopen.cloud (name resolves to CSAIL address)
129.10.5.148    sso.massopen.cloud (VIP)
```

## Public BU (VLAN 105)

-  Subnet: 192.12.185.0/24

```
IP Address       Hostname/Description
192.12.185.1     default gateway
192.12.185.21    api.ocp4-aio.cnv2.massopen.cloud
192.12.185.22    *.apps.ocp4-aio.cnv2.massopen.cloud
192.12.185.32    start cnv loadbalancer range (192.12.185.32/27)
192.12.185.63    end cnv loadbalancer range
192.12.185.64    general dynamic range start
192.12.185.100   general dynamic range stop
192.12.185.101   dhcp1-bm.cnv.massopen.cloud
192.12.185.102   dhcp2-bm.cnv.massopen.cloud
192.12.185.103   provisioner.cnv.massopen.cloud
192.12.185.104   api.cnv.massopen.cloud
192.12.185.105   ingres-lb.cnv.massopen.cloud
192.12.185.106   ns1.cnv.massopen.cloud
192.12.185.107   bootstrap.cnv.massopen.cloud
192.12.185.110   os-mgr-0.cnv.massopen.cloud
192.12.185.111   os-mgr-1.cnv.massopen.cloud
192.12.185.112   os-mgr-2.cnv.massopen.cloud
192.12.185.115   os-wrk-0.cnv.massopen.cloud
192.12.185.116   os-wrk-1.cnv.massopen.cloud
192.12.185.117   os-wrk-2.cnv.massopen.cloud
192.12.185.120   m01.osh.massopen.cloud
192.12.185.121   m02.osh.massopen.cloud
192.12.185.122   m03.osh.massopen.cloud
192.12.185.123   m04.osh.massopen.cloud
192.12.185.124   m05.osh.massopen.cloud
192.12.185.125   m06.osh.massopen.cloud
192.12.185.130   os-sto-0.cnv.massopen.cloud
192.12.185.131   os-sto-1.cnv.massopen.cloud
192.12.185.132   os-sto-2.cnv.massopen.cloud
192.12.185.140   provisioner.cnv2.massopen.cloud
192.12.185.141   api.cnv2.massopen.cloud
192.12.185.142   ns1.cnv2.massopen.cloud
192.12.185.143   lb.cnv2.massopen.cloud
192.12.185.144   bootstrap.cnv2.massopen.cloud
192.12.185.145   openshift-master-0.cnv2.massopen.cloud
192.12.185.146   openshift-master-1.cnv2.massopen.cloud
192.12.185.147   openshift-master-2.cnv2.massopen.cloud
192.12.185.148   openshift-worker-0.cnv2.massopen.cloud
192.12.185.149   openshift-worker-1.cnv2.massopen.cloud

192.12.185.150   provisioner.ocp.massopen.cloud (neu-5-3)
192.12.185.151   api.ocp.massopen.cloud
192.12.185.152   ns1.ocp.massopen.cloud
192.12.185.153   lb.ocp.massopen.cloud
192.12.185.154   bootstrap.ocp.massopen.cloud
192.12.185.155   openshift-master-0.ocp.massopen.cloud (neu-3-11)
192.12.185.156   openshift-master-1.ocp.massopen.cloud (neu-5-10)
192.12.185.157   openshift-master-2.ocp.massopen.cloud (neu-15-9)
192.12.185.158   openshift-worker-0.ocp.massopen.cloud (neu-5-4)
192.12.185.159   openshift-worker-1.ocp.massopen.cloud (neu-15-4)
192.12.185.160   openshift-worker-2-gpu.ocp.massopen.cloud (neu-17-12)

192.12.185.170   provisioner.curator.massopen.cloud (neu-15-29)
192.12.185.171   api.curator.massopen.cloud
192.12.185.172   ns1.curator.massopen.cloud
192.12.185.173   lb.curator.massopen.cloud
192.12.185.174   bootstrap.curator.massopen.cloud
192.12.185.175   os-ctrl-0.curator.massopen.cloud (neu-15-5)
192.12.185.176   os-ctrl-1.curator.massopen.cloud (neu-3-5)
192.12.185.177   os-ctrl-2.curator.massopen.cloud (neu-5-7)

192.12.185.179   my router (so OCP workers can reach nvcr.io)
192.12.185.180   production OCP metallb range start
192.12.185.250   production OCP metallb range stop
192.12.185.251   bu-21-9.massopen.cloud

192.12.185.254   ov3.massopen.cloud (though this name resolves to 129.10.5.103)
```

## CSAIL-10 (VLAN 10)

-  Subnet: 128.52.60.0/22

```
IP Address       Hostname/Description
128.52.60.1      default gateway

128.52.60.2      Start: CSAIL Reserved
128.52.60.20     End: CSAIL Reserved

128.52.60.21     Start: MOC Infrastructure reserved
128.52.60.30     End: MOC Infrastructure reserved

128.52.60.31     Smaug openshift cluster: API Virtual IP
128.52.60.32     Smaug openshift cluster: Ingress Virtual IP
128.52.60.33     ocp-prod cluster: API Virtual IP
128.52.60.34     ocp-prod cluster: Ingress Virtual IP
128.52.60.35     ocp-staging cluster: API Virtual IP
128.52.60.36     ocp-staging cluster: Ingress Virtual IP

128.52.60.50     Start: ocp-staging openshift cluster tentant IP
128.52.60.60     End: ocp-staging openshift cluster tentant IP

128.52.61.0      Start: Smaug openshift cluster Tenant IP (128.52.61.0/25)
128.52.61.127    End: Smaug openshift cluster Tenant IP (128.52.61.0/25)
128.52.61.128    Start: OCP-PROD openshift cluster Tenant IP (128.52.61.128/25)
128.52.61.255    End: OCP-PROD openshift cluster Tenant IP (128.52.61.128/25)

128.52.62.128    Start: CSAIL DHCP (128.52.62.128/25)
128.52.62.255    End: CSAIL DHCP (128.52.62.128/25)
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
10.0.0.9         p9con.massopen.cloud
10.0.0.50        dhcp1-ipmi.cnv.massopen.cloud
10.0.0.51        dhcp2-ipmi.cnv.massopen.cloud
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

## OCT/Umass Switch Management (VLAN 207): 10.1.0.0/24

```
IP Address       Hostname/Description
10.1.0.1         kzn-ipmi-gw

10.1.0.11        OCT1 10G switch (Dell S4048-ON)
10.1.0.12        OCT2 10G switch (Dell S4048-ON)
10.1.0.13        OCT3 10G switch (Dell S4048-ON)
10.1.0.14        OCT4 10G switch (Dell S4048-ON)
10.1.0.15        OCT5 25G switch (Dell S5048F-ON)

10.1.0.55        OCT5 1G Management Switch (Dell S3048-ON)

10.1.0.101       oct-hub-1 (Dell Z9100-ON, R6-PB-C02)
10.1.0.102       oct-hub-2 (Dell Z9100-ON, R6-PB-C02)
10.1.0.103       oct-hub-3 (Dell Z9100-ON, R1-PC-C12)
10.1.0.104       oct-hub-4 (Dell Z9100-ON, R1-PC-C12)
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

## Staging Foreman: 172.17.0.0/19 (VLAN 269)

```
IP Address       Hostname/Description
172.17.0.1       kzn-router.infra.massopen.cloud
172.17.0.3       Foreman
172.17.0.5       Undercloud VM for staging
172.17.0.5       kzn-ipmi-gw.infra.massopen.cloud

```


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

We get this VLAN the same way we get CSAIL-10, i.e., from engage1 switches
and those are directly connected to CSAIL switches.

Statically assigned IPs on this network are:

```
IP Address       Hostname/Description
128.31.20.1      gateway

128.31.20.2      Start: MOC Infrastructure reserved
128.31.20.20     End: MOC Infrastructure reserved

128.31.20.21     Start: ESI floating IP
128.31.20.255    End: ESI floating IP
```

## CSAIL-3803 Public IPs: 128.31.28.0/24 (VLAN 3803)

This network is managed by our [MaaS instance][maas].

Statically assigned IPs on this network are:

```
IP Address       Hostname/Description
128.31.28.9      zabbix
128.31.28.10     kzn-ipmi-gw
128.31.28.11     sso1.massopen.cloud
128.31.28.12     sso2.massopen.cloud
128.31.28.13     sso.massopen.cloud (shared by sso1 and sso2)
128.31.28.14     freeipa2.maas.massopen.cloud

128.31.28.16     maas.massopen.cloud
```

[maas]: https://maas.massopen.cloud/MAAS/#/subnet/1

## Research Ceph (VLAN 252)

```
IP Address       Hostname/Description
192.168.32.1     kzn-rmon1 kzn-rmon1.infra.massopen.cloud
192.168.32.2     kzn-rmon2 kzn-rmon2.infra.massopen.cloud
192.168.32.3     kzn-rmon3 kzn-rmon3.infra.massopen.cloud
192.168.32.4     kzn-ssh.infra.massopen.cloud
192.168.32.5     kzn-rtr.infra.massopen.cloud
192.168.32.6     kzn-zabbix

192.168.35.21    neu-3-21 neu-3-21.infra.massopen.cloud
192.168.35.22    neu-3-22 neu-3-22.infra.massopen.cloud
192.168.35.23    neu-3-23 neu-3-23.infra.massopen.cloud
192.168.35.41    neu-3-41 neu-3-41.infra.massopen.cloud (cache-server)

192.168.37.21    neu-5-21 neu-5-21.infra.massopen.cloud
192.168.37.22    neu-5-22 neu-5-22.infra.massopen.cloud
192.168.37.23    neu-5-23 neu-5-23.infra.massopen.cloud
192.168.37.41    neu-5-41 neu-5-41.infra.massopen.cloud (cache-server)

192.168.47.21    neu-15-21 neu-15-21.infra.massopen.cloud
192.168.47.22    neu-15-22 neu-15-22.infra.massopen.cloud
192.168.47.23    neu-15-23 neu-15-23.infra.massopen.cloud
192.168.47.41    neu-15-41 neu-15-41.infra.massopen.cloud (cache-server)
```
-  HIL/BMI users need to have access to ceph-public network (HIL admin
   operation), add it to their project/nodes/nics and configure an ip, ip is
   calculated as described below (user operations)
-  For client IPs use 192.168.32+Cnum.Unum mask 255.255.224.0, where Cnum is
   cage number and Unum is U number. Cage and U numbers are used in node mame -
   neu-1-21 means Cage 1, U 21 and the ip would be 192.168.33.21
-  Ssh access is by using FreeIpa accounts, nodes are accessible from BMI
   provision subnet (500) by name. Ssh to monitor nodes to get ceph.conf and
   admin keyring.
-  Please use ecrbd pool for rbd images to save disk space. To create an image
   using erasure coded pool for data storage use `rbd create mynewrbdimage
   --data-pool ecrbd --size xxxG`
-  *Be careful with the number of placement groups if creating new pools*

## VPN range

172.31.224.0/19

## Network connections

For 1 GB U number goes to the same port number.

For 10GB U1 goes to ports 1 and 2 on lower Brocade switch, U2 - ports 3,4 up to U20.
U21 goes to ports 1,2 on upper switch and so on.

## Lenovo Ceph - for IBM Power9 (VLAN 210 and 211)

VLAN 210 -  192.168.96.0/19
VLAN 211 -  192.168.128.0/19
