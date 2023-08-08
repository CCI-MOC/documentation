# MOC Public Network Providers

This document describes what external networks the MOC utilizes for public
connectivity and who to contact for support.

## MIT (CSAIL)

There are 4 networks we use from CSAIL. For help, we should contact help@csail.mit.edu

**VLAN 10**

Subnet: 128.52.60.0/22

This was used for various openshift clusters but should be free soon.

As of 8/12/2021, the DHCP range on this network is 128.52.62.128/25.

This network is temporarily on the OCT-CORE switches.

**VLAN 3801**

Subnet: 128.31.20.0/22

This has been assigned to ESI pilot. We get this vlan from the OCT-CORE switches.


**VLAN 3802**

Subnet: 128.31.24.0/22

For Kaizen openstack floating IPs. We don't have a direct connection to CSAIL
switch, but instead it's via NEU->MeetMe->MIT->CSAIL.


**VLAN 3803**

Subnet: 128.31.28.0/24

This is used for MaaS services and hosts, and some infrastructure VMs.
Our Cisco switch in Kumo (Row 4 Pod A Cage) connects to a BU switch which
carries this VLAN in addition to BU's public network (VLAN 105, 192.12.185.0/24).


## BU

We can open a ticket with (BU IS&T)[https://www.bu.edu/tech/contact/] for support.

**VLAN 105**

Subnet: 192.12.185.0/24

Used for all our openshift clusters. We get this from the BU switch in kumo
which also gets us VLAN 3803 from CSAIL.

## NEU

We can email rchelp@northeastern.edu for help.

**VLAN 127**

Subnet: 129.10.5.0/24

Used for all our infrastructure needs like openstack services, monitoring, DNS,
radosgw etc.
