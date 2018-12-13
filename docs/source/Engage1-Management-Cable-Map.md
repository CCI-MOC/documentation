# Engage1 Management Cable Map
*Note - default configuration is access on Vlan1*

### egg1-r4pAc04-mgmt  
* switch management at 10.10.10.1
* credentials: see bitarden Engage1 egg1-r4pAc04

to change anything, type `enable` and type enable password

Port | Mode | VLAN(s) | description | cable color
--- | --- | --- | --- | ---
gig 1/0/1 | access | 4004 | e1-compute-13 p1 | blue
gig 1/0/2 | access | 4004 | e1-compute-14 p1 | blue
gig 1/0/3 | access | 4004 | e1-compute-15 p1 | blue
gig 1/0/4 | access | 4004 | e1-compute-16 p1 | blue
gig 1/0/5 | access | 3040 | egg1-control-02 IPMI | gray
gig 1/0/6 | access | 3040 | -- | --
... | ... | ... | ... | ...
gig 1/0/24 | access | 3040 | -- | --
gig 1/0/25 | trunk | 3040, 4004 | egg1-hil-master p1 | blue
gig 1/0/26 | trunk | 1601,1602,3040,4000,4004 | egg1-services p1 | blue
gig 1/0/27 | trunk | 4000, 4004 | egg1-sdn-01 p1 | blue
gig 1/0/28 | access | 4004 | egg1-control-01 p1 | blue
gig 1/0/29 | access | 4004 | egg1-control-02 p1 | blue
gig 1/0/30 | access | 4004 | egg1-compute-01 p1 | blue
gig 1/0/31 | access | 4004 | egg1-compute-02 p1 | blue
gig 1/0/32 | access | 4004 | egg1-compute-03 p1 | blue
gig 1/0/33 | access | 4004 | egg1-compute-04 p1 | blue
gig 1/0/34 | access | 4004 | egg1-compute-05 p1 | blue
gig 1/0/35 | access | 4004 | egg1-compute-06 p1 | blue
gig 1/0/36 | access | 4004 | egg1-compute-07 p1 | blue
gig 1/0/37 | access | 4004 | egg1-compute-08 p1 | blue
gig 1/0/38 | access | 4004 | egg1-compute-09 p1 | blue
gig 1/0/39 | access | 4004 | egg1-compute-10 p1 | blue
gig 1/0/40 | (default) | -- | -- | -- 
gig 1/0/41 | (default) | -- | -- | --
gig 1/0/42 | (default) | -- | cache-c104-01 | blue
gig 1/0/43 | (default) | 1 | e1-sdn p2 | blue
gig 1/0/44 | trunk | 1601,1602,3040,4000,4004 | egg1-emergency | blue 
gig 1/0/45 | trunk | 3040, 4004 | trunk linke to Kumo switch | green
gig 1/0/46 | trunk | 1601, 1602, 3040, 4004 | trunk line to Dell MRI switch | navy blue
gig 1/0/47 | trunk | 3040, 4004 | trunk line to egg1-r4pAc02-mgmt | red
gig 1/0/48 | access | 3040 | -- | Local Reserved


### egg1-r4pAc04-mgmt-02
* Juniper ex3300
* switch management at 10.10.10.5
* credentials: see bitwarden Engage1 egg1-r4pAc04-mgmt-02

CLI for Juniper is not very intuitive or similar to Cisco. When you log in via SSH you must type "CLI" to get to the switch CLI

Can be managed via web GUI at 10.10.10.5, same login credentials

The intention is to use this switch for the IPMI network (VLAN 3040) only.  All ports are currently set to access 3040.  If we ever need an additional managed switch somewhere, we could use this and substitute a cheap non-managed switch.

Port | Mode | VLAN(s) | description | cable color
--- | --- | --- | --- | ---
gig 1/0/0 | access | 3040 | egg1-hil-master IPMI | gray
gig 1/0/1 | access | 3040 | egg1-services IPMI | gray
gig 1/0/2 | access | 3040 | egg1-sdn-01 IPMI | gray
gig 1/0/3 | access | 3040 | egg1-control-01 IPMI | gray
gig 1/0/4 | access | 3040 | egg1-control-02 IPMI | gray
gig 1/0/5 | access | 3040 | egg1-compute-01 IPMI | gray
gig 1/0/6 | access | 3040 | egg1-compute-02 IPMI | gray
gig 1/0/7 | access | 3040 | egg1-compute-03 IPMI | gray
gig 1/0/8 | access | 3040 | egg1-compute-04 IPMI | gray
gig 1/0/9 | access | 3040 | egg1-compute-05 IPMI | gray
gig 1/0/10 | access | 3040 | egg1-compute-06 IPMI | gray
gig 1/0/11 | access | 3040 | egg1-compute-07 IPMI | gray
gig 1/0/12 | access | 3040 | egg1-compute-08 IPMI | gray
gig 1/0/13 | access | 3040 | egg1-compute-09 IPMI | gray
gig 1/0/14 | access | 3040 | egg1-compute-10 IPMI | gray
gig 1/0/15 | access | 3040 | *future compute-11* | --
gig 1/0/16 | access | 3040 | egg1-emergency IPMI | --
gig 1/0/17 | access | 3040 | RBridge-104 console | --
gig 1/0/18 | access | 3040 | r4pAc02 PDUL | gray
gig 1/0/19 | access | 3040 | r4pAc02 PDUR | gray
gig 1/0/20 | access | 4000 | egg1-r4pAc04-of01 | gray
gig 1/0/21 | access |  3040 | cache-c104-01 | gray (?)
gig 1/0/22 | access | 3040 | -- | --
... | ... | ... | ... | 
gig 1/0/45 | access | 3040 | -- | --
gig 1/0/46 | access | 3040 | to e1-r4pAc01-mgmt p48 | gray
gig 1/0/47 | access | 3040 | -- | Local Reserved


### egg1-r4pAc02-mgmt  
* switch management at 10.10.10.2
* credentials: see bitwarden Engage1 egg1-r4pAc02-mgmt

to change anything, type `enable` and type enable password

Port | Mode | VLAN(s) | description | cable color
--- | --- | --- | --- | ---
gig 1/0/1 | access | 3040 | egg1-ceph-01 IPMI | gray
gig 1/0/2 | access | 3040 | egg1-ceph-02 IPMI | gray
gig 1/0/3 | access | 3040 | egg1-ceph-03 IPMI | gray
gig 1/0/4 | access | 3040 | egg1-ceph-04 IPMI | gray
gig 1/0/5 | access | 3040 | egg1-ceph-05 IPMI | gray
gig 1/0/6 | access | 3040 | egg1-ceph-06 IPMI | gray
gig 1/0/7 | access | 3040 | egg1-ceph-07 IPMI | gray
gig 1/0/8 | access | 3040 | egg1-ceph-08 IPMI | gray
gig 1/0/9 | access | 3040 | egg1-ceph-09 IPMI | gray
gig 1/0/10 | access | 3040 | egg1-ceph-10 IPMI | gray
gig 1/0/11 | access | 3040 | egg1-ceph-mon01 IPMI | gray
gig 1/0/12 | access | 3040 | egg1-ceph-mon02 IPMI | gray
gig 1/0/13 | access | 3040 | egg1-ceph-mon03 IPMI | gray
gig 1/0/14 | access | 3040 | RBridge-101 console | gray
gig 1/0/15 | access | 3040 | RBridge-102 console | gray
gig 1/0/16 | access | 3040 | RBridge 103 console | gray
gig 1/0/17 | (default) | -- | PDUL | --
gig 1/0/18 | (default) | -- | PDUR | --
gig 1/0/19 | (default) | -- | --
gig 1/0/20 | (default) | -- | --
gig 1/0/21 | (default) | -- | --
gig 1/0/22 | (default) | -- | --
gig 1/0/23 | (default) | -- | --
gig 1/0/24 | (default) | -- | -- 
gig 1/0/25 | access | 4004 | egg1-ceph-01 p1 | blue
gig 1/0/26 | access | 4004 | egg1-ceph-02 p1 | blue
gig 1/0/27 | access | 4004 | egg1-ceph-03 p1 | blue
gig 1/0/28 | access | 4004 | egg1-ceph-04 p1 | blue
gig 1/0/29 | access | 4004 | egg1-ceph-05 p1 | blue
gig 1/0/30 | access | 4004 | egg1-ceph-06 p1 | blue
gig 1/0/31 | access | 4004 | egg1-ceph-07 p1 | blue
gig 1/0/32 | access | 4004 | egg1-ceph-08 p1 | blue
gig 1/0/33 | access | 4004 | egg1-ceph-09 p1 | blue
gig 1/0/34 | access | 4004 | egg1-ceph-10 p1 | blue
gig 1/0/35 | access | 4004 | egg1-ceph-mon01 p1 | blue
gig 1/0/36 | access | 4004 | egg1-ceph-mon02 p1 | blue
gig 1/0/37 | access | 4004 | egg1-ceph-mon03 p1 | blue
gig 1/0/38 | (default) | -- | -- | -- 
gig 1/0/39 | (default) | -- | -- | -- 
gig 1/0/40 | (default) | -- | -- | -- 
gig 1/0/41 | (default) | -- | -- | --
gig 1/0/42 | (default) | -- | -- | --
gig 1/0/43 | (default) | -- | -- | --
gig 1/0/44 | (default) | -- | -- | --
gig 1/0/45 | (default) | -- | -- | --
gig 1/0/46 | (default) | -- | -- | --
gig 1/0/47 | (default) | -- | -- | --
gig 1/0/48 | access | 3040 | Local Reserved | n/a

