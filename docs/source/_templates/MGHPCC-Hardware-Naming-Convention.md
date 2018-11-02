###General convention

The goals of this naming convention:  
1. names are always unique
2. names are reasonably informative
3. names are not outrageously long
4. convention is flexible enough to apply to different situations and deployments
5. convention is simple enough that you just know what to name any new hardware

######standalone servers:
* \<ENVIRONMENT\> - \<PURPOSE\> [- \<NUMBER\>]      

######standalone switches:
* \<ENVIRONMENT\> - \<LOCATION\> - \<PURPOSE\> [- \<NUMBER\>]

######stacked switches:
* \<ENVIRONMENT\> - \<LOCATION\> - \<PURPOSE\> - s\<SLOT NUMBER\>

######compute chassis/blades and storage controller/enclosure setups:
* \<ENVIRONMENT\> - \<PURPOSE/DESCRIPTION\> - CH\<NUMBER\> (for storage, 'S' instead of 'CH')
* \<ENVIRONMENT\> - CH\<NUMBER\> - blade\<NUMBER\>  (for storage enclosures, E instead of 'blade') 

Key:   
**ENVIRONMENT:** a short (3-4 chars) code indicating the environment, e.g. KZN, KUMO, EGG1   
**PURPOSE:** what's it for? server options: compute, ceph, services, etc. switch options: mgmt, tor, test (or dev)    
**LOCATION:** row/pod/cabinet location in format r0pXc00   
**NUMBER:** omitted when there is only one this node type in the environment.  
* might also hold additional meaning, e.g. which blade slot in a chassis
* for stacked switches, this could indicate the 'slot number' (possibly preceded by s in case we ever have 2 management switches in one rack)

**NOTE:** The established Engage1 Brocade Fabric switches should continue to use their existing naming convention using RBridgeIDs which match cabinet numbers; existing convention works and is self contained, so changing it would be a lot of work for little gain.
 
###Examples
Here's what this would look like in our existing deployments: 

######NEU/KAIZEN:
Note: NEU/Kaizen will not be brought into the naming scheme at this time, but might be if there is an opportunity in future.

old name | new name 
 ----| ---- 
haas-master | kzn-hil-master
node-39, compute-39 | kzn-services
node-47 | kzn-control-01
compute-01, cisco-01 | kzn-compute-01
emergency-recovery | kzn-emergency
   
######Engage1
Note: The existing Brocade Fabric will not be brought into this scheme; it already has a naming convention that works, and we wouldn't gain much by changing it.

Old systems may be brought over slowly 

| old name | new name | notes
| ---- | ---- | ----
moc-jun3300-01 | egg1-r4pAc04-mgmt | if stacked, this might look like egg1-r4pAc04-MGMT-s01
 (n/a) | egg1-r4pAc02-mgmt | stacked, this might be egg1-r4pAc04-mgmt-s02
moc-services01 | egg1-services | 
engage1-emergency | egg1-emergency | 
moc-haas01 | egg1-hil-master | 
moc-sdn01 | egg1-sdn | 
moc-control01 | egg1-control-01 |
moc-compute01 | egg1-compute-01  | 
ceph-lenovo01 | egg1-ceph-01 |
ceph-quanta01 | egg1-ceph-mon01 | 
(n/a) | egg1-r4pAc04-oflow01 | Yossi's OpenFlow test switch

######Kumo

name | notes
---- | ----
kumo-r4pAc23-TOR | nexus 3548
kumo-r4pAc23-MGMT | catalyst 3650 
kumo-services | 
kumo-emergency |
kumo-compute-CH01 | Dell M1000e chassis
kumo-hil-master | whichever Dell blade is the hil master
kumo-CH01-blade01 | the other 15 blades; # should match blade slot
kumo-storage-CTL01 | Dell R720 server - could be kumo-ceph-CTL01 if this will be Ceph storage
kumo-E01-S01 | Dell storage enclosure

### Labeling Conventions
* Each piece of hardware should be labeled with its name on both sides, so that a label is visible from both hot and cold aisles.
* Color code:    
    * blue cables = in band network
    * gray cables = out of band / IPMI network
    * black cables = 10GB network cables (do not use any black 1G cables)
    * other colors = a special cable, for example connecting 2 switches
* Gray cables do not need labels, unless they have a nonstandard port configuration (in which case, should it really be a gray cable?)  Still, reasonable care should be taken to return them to the documented ports if they are unplugged.
* Blue cables should always have labels.
* Network cables connecting to a switch in the same rack: 
```
     // Example 
      egg1-r4pAc04-mgmt p25
      egg1-hil-master p1
```
* Network cables connecting two switches in different racks: 
```
      // Example 
      egg1-r4pAc04-mgmt p48
      kumo-r4pAc23-mgmt p47
```

* Network cables connecting two different racks when one or both endpoints is not a switch (this should be rare): 
```
      // Example with switch and 2U server
      r4pAc04 - r4pAc02 
      egg1-r4pAc04-mgmt u42 p16
      egg1-ceph-01 u01-02 p1
```

* Power cord.  If the cord is short and visible enough that tracing it is trivial, one label at the male end is OK, otherwise it should have labels at both ends.
```
      // Example 
      egg1-services PS1
      PDUB-L3-P8
```

