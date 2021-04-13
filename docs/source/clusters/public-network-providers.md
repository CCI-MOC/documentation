## MOC Public Network Providers

This document describes what external networks the MOC utilizes for public
connectivity and who to contact for support.

### MIT (CSAIL)

There are 4 networks we use from CSAIL. For help, we should contact help@csail.mit.edu

**VLAN 10**

Subnet: 128.52.60.0/22

We used this for infrastructure in engage1 but we have to use CSAIL's DHCP since
they like to control this network, which makes this less desirable.

Our brocade switches in BU cages 2 and 4 connect to the CSAIL switch to access
this network.


**VLAN 3801**

Subnet: 128.31.20.0/22

We used this for engage1. Our brocade switches in BU cages 2 and 4 connect to
the CSAIL switch to access this network.


**VLAN 3802**

Subnet: 128.31.24.0/22

For Kaizen openstack floating IPs. We don't have a direct connection to CSAIL
switch, but instead it's via NEU->MeetMe->MIT->CSAIL.


**VLAN 3803**

Subnet: 128.31.28.0/24

This is used for MaaS services and hosts, and some infrastructure VMs.
Our Cisco switch in Kumo (Row 4 Pod A Cage) connects to a BU switch which
carries this VLAN in addition to BU's public network (VLAN 105, 192.12.185.0/24).

**Note**

We have no servers racked in MIT space.
The cluster we call enage1 at MOC is racked in BU cages 2 and 4 (Row 4 Pod A).
We have 4 brocade switches racked in those cages and they are part of a brocade fabric
where the rest of the brocade switches (5) are racked in MIT cages. We should figure who
gets dibs on those switches. The brocade switches that connect to CSAIL are in our cages so that's good.


### BU

We can open a ticket with (BU IS&T)[https://www.bu.edu/tech/contact/] for support.

**VLAN 105**

Subnet: 192.12.185.0/24

Used for all our openshift clusters. We get this from the BU switch in kumo
which also gets us VLAN 3803 from CSAIL.

### NEU

We can email rchelp@northeastern.edu for help.

**VLAN 127**

Subnet: 129.10.5.0/24

Used for all our infrastructure needs like openstack services, monitoring, DNS,
radosgw etc.
