Instructions for [[Accessing Northeastern Cluster]]

## Projects
* [[Kaizen Production]]
* [[Monitoring in NEU Env.]]
* [[Staging Area]]
* [VMs running on physical nodes](https://github.com/CCI-MOC/moc/wiki/VMs-running-on-nodes)
* *add links for other projects hosted at NEU*

## Equipment
[[NEU Equipment Sign-Out]]

##### SERVERS
* Cisco UCS C220 M3 SSF (x48). See [this file](https://github.com/CCI-MOC/moc/wiki/CiscoConfiguration.pdf) for list of configuration components. 
* numbers 1-24 in rack 17, 25-48 in rack 19.  Numbered from bottom of rack up.
* More information specific to the [[Cisco UCS C220]] nodes
* Internal Storage
   * All nodes have a single 300GB drive in slot 1
   * These nodes also have an additional 1TB drive in slot 2: 
       * node 6, nodes 14-24, nodes 39-47
   * Node 48 (the haas master) has 3 additional 1TB drives 
* IPMI access [[VM Setup for Cisco IPMI Access]]
* There is also a SuperMicro emergency box
   * default IPMI credentials: ADMIN/ADMIN

##### SWITCHES
* Cisco Nexus 5672 (x2)
* More information at [[Cisco Switches (NEU cluster)]]
* Servers are connected to the switch ports in order 
    * on switch R3-C17, server 1 connects to port 1, server 2 to port 2, and so on up to server 24 to port 24.
    * on switch R3-C19, server 25 connects to port 1, server 48 to port 24.

##### STORAGE
   * [[Fujitsu CD10000]]

## Networks
* [[Northeastern Cluster Network Documentation]]
* [[/24 plan (4/2016)]]

## Setup/Configuration
* [[Kilo/RHEL-OSP-7 Deployment]]
* [[Icehouse/Red Hat 7.0 Deployment Experimentation]]
* [[Installing CentOS 7 on a node hard drive]]

## Miscellaneous
* [[Misc. errors/problems]]
* [List of operation tasks to be discussed with Northeastern University Operation at MGHPCC](https://github.com/CCI-MOC/moc-public/wiki/Ops-tasks-in-Northeastern-University-MOC-deployment)

![](NUManagementNetworkTopology.png)
![](NUclusterNetworkTopology.png)

Updates:
(5 Aug. 2016) The top connections between R3-C17 and R3-C19 switches (VLAN3) are not being used (configured) right now. Thus, it would be possible to reclaim two ports from each of the two switches.   


