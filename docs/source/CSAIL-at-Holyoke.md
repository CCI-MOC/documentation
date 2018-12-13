# CSAIL at Holyoke
All of this is still up in the air...

CSAIL's network connection to Holyoke will run via the metro ring to 300 Bent St. From there it will take both paths (short/west and long/east) paths on the statewide ring to Holyoke.

To us this will look like a pair of 10Gbase-LR Ethernet links.  Each link will terminate on a different switch at the CSAIL end, and we'll use two routed VLANs on each link, one for "public" network traffic and one for "private" (management) traffic.

Everything that runs over this connection will be treated as "inside CSAIL" for access-control purposes, and its outside connectivity will run through CSAIL's border router.

Here are the network assignments as currently proposed:
* **128.52.64.0/18** -- primary CSAIL network... undecided whether or how this will be broken up between clients and infrastructure (vlan tag 2116)
* **192.12.11.0/24** -- external CSAIL network, routed directly to outside, used for CSAIL infrastructure only (vlan tag 11)
* **172.16.0.0/30, 172.16.0.4/30** -- private fiber links (vlan tags 2118, 2120)
* **172.19.0.0/16** -- private management network (vlan tag 2116)
* **TBD/30, TBD/30** -- public fiber links (vlan tags 2117, 2119)
* **128.31.x.x/TBD** -- a public network for non-CSAIL infrastructure/users? need and configuration as yet undetermined

Also TBD is what device at MGHPCC the CSAIL links will terminate on... for proof-of-concept this is going to be a Juniper EX4200, but they only have two 10G ports so that doesn't provide the level of connectivity we need for MOC.

