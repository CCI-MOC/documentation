## MOC VLAN Distribution
This document lays out how the VLANs are distributed between our various clusters.

It also talks (briefly) about how the hardware, and the infrastructure services we are running.

There are (or will be) environment specific documents describing the networking setup with VLAN, network and host information.

### oVirt
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
 1. HIL server (maybe multiple for different environments)
 1. Gateway/router for HIL/BMI services (again, could be multiple)
 1. HIL client (again, could be multiple)
 1. DNS servers for massopen.cloud, openapp.cloud, mocapps.cloud
    - 1 Management instance and 2 DNS servers for massopen.cloud
    - same thing for the other 2 domains.
    - a total of 6 VMs
 1. Additional internal DNS
 1. Kaizen's RadosGW will be here.
 1. cacti - for statistics
 1. Nagios - for monitoring.
 1. Other ssh gateways.
 1. IPMI gateways.

Each environment gets around 300 to 400 VLANs which will leave room for expansion for more environments. \
Below is the detailed distribution of the VLANs.

### MOC VLANs
**VLANs common to all environments**:

| VLAN(s)   | Description                                                                  | Subnet(s)       |
| --------- | ---------------------------------------------------------------------------- | --------------- |
| 1 to 200  | Reserved by University's IS&T for providing public IPs                       | -               |
| 127       | NEU Public IP for infrastructure                                             | 129.10.5.0/24   |
| 3800      | For connecting environments, but we don't know about bandwidth, or the route | -               |
| 3801-3803 | CSAIL Floating IPs                                                           | 128.31.XX.YY/22 |

### Kaizen
**Hardware Summary**
 -  25 openstack computes (Dell IBs)
 -  3 oVirt nodes (Dell IBs)
 -  48 Cisco nodes. (Will be available for Elastic HW)
 -  3+2 openstack controllers (Dell SBs)
 -  8 ceph OSDs (right now, we can change this).
 -  64 Dell SB nodes that can be elastic with 2 drives each which we can redistribute. Extra 5 drives for spares.
 Total Elastic Nodes = 48 Cisco + 64 Dell SB  = 112 nodes.

**VLANs**
 -  VLAN range : 201 to 700 (total 399 VLANs)
 -  201 to 600 - 10G networks
 -  601 to 700 for IPMI/management on 1G networks.

| VLAN | Description                                             | Subnet         |
| ---- | ------------------------------------------------------- | -------------- |
| 127  | Openstack public facing services (horizon, keystone)    | 129.10.5.0/24  |
| 201  | Foreman Provisioning. SNMP for OpenStack and Ceph       | 172.16.0.0/19  |
| 202  | OpenStack internal API)                                 | 172.16.32.0/19 |
| 203  | OpenStack tenant network                                | 172.16.64.0/19 |
| 204  | Intranet (routable to internet). SNMP for client nodes. | 172.16.96.0/19 |
| 205  | Gluster/VM migration - oVirt                            | 192.168.0.0/24 |
| 206  | OStack isolation native vlan for trunk only ports       | N/A            |
| 207  | For OCT/UMass Switch Management                         | 10.1.0.0/24    |
| 208  | ESI control plane                                       | --             |
| 209  | Openshift Internal (Baremetal 4.x)                      | --             |
| 210  | NFS for zero cluster                                    | 10.253.0.0/23  |
| 249  | Ceph Cluster (internal)                                 |192.168.40.0/23 |  
| 250  | Ceph public (for clients)                               | 192.168.0.0/19 |
| 259  | Staging - Foreman                                       | 172.17.0.0/19  |
| 270  | Staging - Internal API                                  | To be decided  |
| 271  | Staging - Tenant Network                                | To be decided  |
| 272  | Staging - Public Network                                | To be decided  |
| 273  | Staging - OS Stack isolation for trunk only ports       | To be decided  |
| 911  | For OCT/UMass nodes IPMI                                | 10.2.0.0/19    |
| 912  | For OCT/UMass nodes IPMI for operate first              | 10.3.0.0/19    |
| 3802 | OpenStack Tenant Floating IPs                           | 128.31.24.0/22 |

IPs in VLAN 204 will be assigned based on rack and unit number, rest will be regular DHCP.
Eg; a node in cage 3 unit 20 will get an ip like 172.16.96+3.20

VLANs 251 to 600 will be reserved for HIL and BMI. If each tenant needs at least 5 VLANs for their project in HIL,
then with approximately 350 VLANs we can support a decent number of tenants with room for expansion.

Kaizen Floating IP Split:
 -  3802 (Kzn) is 128.31.24.0/22 (i.e., 2K IP)
 -  Start 128.31.24.129 - End 128.31.27.254

Kzn split
 -  Old: 128.31.24.129,128.31.25.255
 -  New: 128.31.26.0,128.31.27.254

### Engage1
**Hardware Summary**
 -  12 OpenStack computes right now. 3 nodes with GPUs will be moved to Kaizen so we'll have 9 OpenStack computes.
 -  2 OpenStack Controllers
 -  3 monitors and 10 OSDs.
 -  4 services nodes. And some more extra nodes.

**VLANs**
 -  VLAN Range: 701 to 1000 (total 299 VLANs)
 -  701 to 900 - 10G networks.
 -  901 to 1000 for IPMI/management on 1G networks.

| VLAN | Description                                             | Subnet                         |
| ---- | ------------------------------------------------------- | ------------------------------ |
| 10   | Openstack public facing services (horizon, keystone)    | DHCP from CSAIL 128.52.60.0/22 |
| 701  | Foreman Provisioning. SNMP for OpenStack and Ceph       | 172.17.0.0/19                  |
| 702  | OpenStack internal API)                                 | 172.17.32.0/19                 |
| 703  | OpenStack tenant network                                | 172.17.64.0/19                 |
| 704  | Intranet (routable to internet). SNMP for client nodes. | 172.17.96.0/19                 |
| 749  | Ceph Cluster (internal)                                 | --                             |
| 750  | Ceph public (for clients)                               | --                             |
| 3801 | OpenStack Tenant Floating IPs                           | 128.31.20.0/22                 |

VLAN 751 to 1000 will be reserved for HIL and BMI.

### Kumo
**Hardware Summary**
 -  16 Dell Blades
 -  1 kumo-storage hosting all services.
 -  1 kumo-services, and 1 kumo-emergency.

**VLANs**
 -  VLAN Range: 1001 to 1300 (total 299 VLANs)
 -  1001 to 1200 for 10G stuff
 -  1201 to 1300 for IPMI/Management on 1G networks.

| VLAN | Description                                             | Subnet          |
| ---- | ------------------------------------------------------- | --------------- |
| 105  | Public IPs from BU                                      | 192.12.185.0/24 |
| 1004 | Intranet (routable to internet). SNMP for client nodes. | 172.18.0.0/19   |
| 3808 | Public IPs                                              | 128.31.28.0/24  |

 -  Rest of the VLANs will be reserved for HIL in this environment.
 -  This leaves us with VLANs 1300 to 4094 for expansion in the future


### NERC


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


### OCT/cloudlab

Cloudlab side's dataplane is fully dynamic, they'd block out MOC vlans to avoid conflict, and agree on a range. The proposed range right now is < 1024 for cloudlab, > 1024 for OCT (with cloudlab blocking MOC existing vlan IDs).

| VLAN | Description                                             | Subnet          |
| ---- | ------------------------------------------------------- | --------------- |
| 84   | cloudlab-1 UMass Public IPs                             | 198.22.255.0/24 |

### MOC connections to OCT/Umass

| Description                         | MOC switchport                                          | OCT/Umass switchport                   |
| ----------------------------------- | ------------------------------------------------------- | -------------------------------------- |
| 40G cable for data plane            | Cage 15 Unit 43 Brocade (Fo 2/0/52) (Port Channel 2)    | OCT-HUB-2 (Port Channel 11, Fo 1/27/1) |
| 40G cable for data plane            | Cage 15 Unit 42 Brocade (Fo 1/0/52) (Port Channel 2)    | OCT-HUB-1 (Port Channel 11, Fo 1/27/1) |
| 1G cable for IPMI network (disabled)| Cage 15 Unit 44 port 45                                 |         -                              |


While the 1G connection still exists, we have disabled it since we can reach the OCT IPMI network over out data network.