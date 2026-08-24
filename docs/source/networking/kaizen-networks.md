# MOC Networks

This documents lists out networks on MOC. This list is NOT COMPLETE.

## Public Neu (VLAN 127)

-  Subnet: 129.10.5.0/24
```
IP Address       Hostname/Description
129.10.5.1       Firewall VIP IP
129.10.5.2       fw1 IP
129.10.5.3       fw2 IP
129.10.5.4       NAT IP on firewalls

129.10.5.101     oac-dev-workload
129.10.5.102     oac-dev-workload-oauth

129.10.5.118     techsquare.massopen.cloud

129.10.5.140     esi-undercloud.massopen.cloud
129.10.5.141     esi-controller-0.massopen.cloud
129.10.5.142     esi-controller-1.massopen.cloud
129.10.5.143     esi-controller-2.massopen.cloud
129.10.5.144     esi-vip

```

## IPMI network (VLAN 911, ESI VLAN): 10.2.0.0/19

```
IP Address       Hostname/Description
10.2.0.1         firewall-vip
10.2.0.1         fw1
10.2.0.1         fw2

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

## IPMI network (VLAN 912): 10.3.0.0/19


```
IP Address       Hostname/Description
10.3.0.1         firewalls
10.3.0.1         fw1
10.3.0.1         fw2

10.3.10.114      oac-prod-infra-1
10.3.10.115      oac-prod-infra-2
10.3.10.116      oac-prod-infra-3

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

## Ceph cluster/replication (VLAN 249)

-  Subnet: 192.168.40.0/23

```
IP Address  Hostname/Description
TBD
```
## Resarch Ceph client (VLAN 250)

-  Subnet: 192.168.0.0/19

```
IP Address       Hostname/Description
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

```

## Provision/SNMP network

-  172.16.0.0/19, moc-infra OpenShift

```
IP Address       Hostname/Description
172.16.0.1       firewall VIP
172.16.0.2       fw1
172.16.0.3       fw2

172.16.0.100     moc-infra API
172.16.0.101     moc-infra ingress
172.16.0.223     moc-infra-1
172.16.0.237     moc-infra-2
172.16.0.238     moc-infra-2

172.16.1.1       metallb-start
172.16.1.254     metallb-end
```

## Intranet: 172.16.96.0/19 (VLAN 204)

```
IP Address       Hostname/Description
172.16.96.1       firewall VIP
172.16.96.2       fw1
172.16.96.3       fw2

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
