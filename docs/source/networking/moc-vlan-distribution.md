# MOC VLAN Distribution
This document lays out how the VLANs are distributed between our various clusters.

It also talks (briefly) about how the hardware, and the infrastructure services we are running.

There are (or will be) environment specific documents describing the networking setup with VLAN, network and host information.

## oVirt
We have a 3 node oVirt setup for hosting crucial services like DHCP, DNS, FreeIPA, Foreman etc.

All these services are highly available VMs.

**Configuration of oVirt nodes**:

| Name               | RAM    | Cores/threads | Storage |
| ------------------ | ------ | ------------- | ------- |
| ov1.massopen.cloud | 384 GB | 24/48         | 10X1TB  |
| ov2.massopen.cloud | 384 GB | 20/40         | 10X1TB  |
| ov3.massopen.cloud | 384 GB | 20/40         | 10X1TB  |

**Services that will be hosted on the oVirt setup**
 1. FreeIPA server (one will be duplicated at BU),
 1. Foreman (might be multiple for different env)
 1. RHEL repo server, just 1 for all environemtns. Public IP with limited access.
 1. Gateway/routers
 1. DNS servers for massopen.cloud
    - 1 Management instance and 2 DNS servers for massopen.cloud
    - same thing for the other 2 domains.
    - a total of 6 VMs
 1. Additional internal DNS
 1. Kaizen's RadosGW will be here.
 1. Nagios - for monitoring.
 1. IPMI gateways.

Each environment gets around 300 to 400 VLANs which will leave room for expansion for more environments. \
Below is the detailed distribution of the VLANs.

## MOC VLANs
**VLANs common to all environments**:

| VLAN(s)   | Description                                                                  | Subnet(s)       |
| --------- | ---------------------------------------------------------------------------- | --------------- |
| 1 to 200  | Reserved by University's IS&T for providing public IPs                       | -               |
| 127       | NEU Public IP for infrastructure                                             | 129.10.5.0/24   |
| 3801      | CSAIL Floating IPs                                                           | 128.31.20.0/22  |

## Kaizen (R4-PA-C02 and C04)
**Hardware Summary**
 -  3 oVirt nodes (Dell IBs)
 -  10 ceph OSD servers (right now, we can change this).

**VLANs**
 -  VLAN range : 201 to 700 (total 399 VLANs)
 -  201 to 600 - 10G networks
 -  601 to 700 for IPMI/management on 1G networks.

| VLAN | Description                                             | Subnet         |
| ---- | ------------------------------------------------------- | -------------- |
| 127  | Openstack public facing services (horizon, keystone)    | 129.10.5.0/24  |
| 201  | Foreman Provisioning. SNMP for OpenStack and Ceph       | 172.16.0.0/19  |
| 204  | Intranet (routable to internet). SNMP for client nodes. | 172.16.96.0/19 |
| 205  | Gluster/VM migration - oVirt                            | 192.168.0.0/24 |
| 208  | ESI control plane                                       | --             |
| 211  | New England Storage Exchange (NESE)                     | 10.120.0.0/22  |
| 213  | Ceph Cluster iSCSI                                      | 10.21.0.0/22   |
| 249  | Ceph Cluster (internal)                                 |192.168.40.0/23 |
| 250  | Ceph public (for clients)                               | 192.168.0.0/19 |
| 290  | OKD internal network                                    | 10.5.0.0/24    |
| 911  | For OCT/UMass nodes IPMI                                | 10.2.0.0/19    |
| 912  | For OCT/UMass nodes IPMI for operate first              | 10.3.0.0/19    |
| 913  | For OKD nodes in OCT4 IPMI                              | 10.4.0.0/19    |
| 3801 | ESI Tenant Floating IPs                                 | 128.31.20.0/22 |


VLANs 251 to 600 will be reserved for ESI.


## Kumo (R4-PA-C23)
**Hardware Summary**
 -  16 Dell Blades
 -  1 kumo-storage hosting all services.
 -  1 kumo-services, and 1 kumo-emergency.

**VLANs**
| VLAN | Description                                             | Subnet          |
| ---- | ------------------------------------------------------- | --------------- |
| 105  | Public IPs from BU                                      | 192.12.185.0/24 |


## NERC

| VLAN | Description                                             | Subnet          |
| ---- | ------------------------------------------------------- | --------------- |
| 2476 | NERC-Admin network 1->eth0:provisioning network(vlan 100)-deploying images/DHCP> PAT out | 10.255.0.0/24 |
| 2472 | eth0.101: internal api (vlan 101) - internal to node | 172.18.0.0/23|
| 2473 | eth0.102: tenant private (vlan 102) - VMs sit here, private network/VXLAN | 172.18.4.0/23  |
| 2477 | NERC - OBM/MGMT Network 1 -> eth1: management/ipmi - undercloud to DRAC/BMC 10.255 | 10.255.1.0/24 |
| 2478 | eth2: storage (vlan 103) - ceph/jumboframe? 10.255 | 10.255.2.0/24  |
| 2471 | *eth3: external network (vlan 200) - public API / endpoints | 140.247.152.0/27  |
| 2470 | *eth4: floating ip (vlan 201) - tenant public IPs | 140.247.152.128/25  |

undercloud needs these - 2476, 2477 (edited)
NERC checking with nNck see if it's possible get 2476 to be a 192.168.24.0/24 CIDR (edited)


## OCT/cloudlab

Cloudlab side's dataplane is fully dynamic, they'd block out MOC vlans to avoid conflict, and agree on a range. The proposed range right now is < 1024 for cloudlab, > 1024 for OCT (with cloudlab blocking MOC existing vlan IDs).

| VLAN | Description                                             | Subnet          |
| ---- | ------------------------------------------------------- | --------------- |
| 84   | cloudlab-1 UMass Public IPs                             | 198.22.255.0/24 |

## MOC connections to OCT/Umass

| Description                         | MOC switchport                                          | OCT/Umass switchport                   |
| ----------------------------------- | ------------------------------------------------------- | -------------------------------------- |
| 40G cable for data plane            | Cage 15 Unit 43 Brocade (Fo 2/0/52) (Port Channel 2)    | OCT-HUB-2 (Port Channel 11, Fo 1/27/1) |
| 40G cable for data plane            | Cage 15 Unit 42 Brocade (Fo 1/0/52) (Port Channel 2)    | OCT-HUB-1 (Port Channel 11, Fo 1/27/1) |
| 1G cable for IPMI network (disabled)| Cage 15 Unit 44 port 45                                 |         -                              |


While the 1G connection still exists, we have disabled it since we can reach the OCT IPMI network over out data network.
