This document describes new haas development setup in PRB. The HaaS
master is essentially unchanged (and in fact needs to be updated to
conform to our new system administration standards).

We're using the sun blades as worker nodes, and the dell switch,
with the ports used as follows:

* 1,3,5,7: nic #0 on the first 4 nodes (counting from the top)
* 2,4,6,8: nic #1 on the first 4 nodes.
* 9: haas master eth1 (outside world); vlan 2
* 10: ipmi daisychain
* 11: haas master eth0 (trunk nic)
* 12: haas master eth3 (switch control)
* 13-20: nics on the remainder of the nodes
* 24: outside world; vlan 2

The Switch has ip address 192.168.3.245, which is visible on vlan 1.
Accordingly, port 12 (the haas master's switch control nic) is also on
vlan 1.

Vlan 2 is exposed to the outside world. Right now this includes only the
haas master's external nic.
