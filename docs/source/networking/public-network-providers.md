# MOC Public Network Providers

This document describes what external networks the MOC utilizes for public
connectivity and who to contact for support.

## MIT (CSAIL)

We now get a single /22 from CSAIL. For help, we should contact help@csail.mit.edu

**VLAN 3801**

Subnet: 128.31.20.0/22

This has been assigned to ESI pilot. We get this vlan from the OCT-CORE switches.

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
