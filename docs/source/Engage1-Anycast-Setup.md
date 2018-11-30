# Engage1 Anycast Setup  
1. [Overview](#overview)  
2. [VLAN Reservations](#vlan-reservations)  
3. [IP Space Allocations](#ip-space-allocations)  
4. [Step By Step Configuration](#step-by-step-configuration)  
5. [DHCP Setup](#dhcp-setup)

### Overview
The purpose of the Anycast setup is to allow compute nodes belonging to some arbitrary number of independent clusters to access their nearest Intel Cache Server via a fixed IP address.  

In addition, if a cache server fails, the nodes will be redirected to a 'backup' cache server without perceiving the difference.

The network was configured based on [this document]<!--(documents/Hadoop_Arch_v2_2016-11-09.docx)--> prepared by Rob Montgomery at Brocade.  The configuration steps below are from that document, although IP addresses and VLAN #'s have been changed to reflect the actual implementation

### VLAN Reservations
The following VLANs are reserved for use in the Anycast setup

| VLAN | Description | Devices |
| --- | --------------------------- | ------------------------ | 
| 2000 | Cache Transit VLAN | All RBridges and Cache Nodes |
| 2001-2032 | Cache Node VLANs | One per cache node + RBridge pair |
| 2100-2699 | Compute VLANs | Every RBridge; one per compute cluster |

The Cache Node Vlans are assigned by adding 2000 to the cabinet number of the RBridge and its cache node. So, RBridge-7 + Cache Node 7 are on VLAN 2007. 

There are only 24 racks in the pod, but for now extra VLANS have been blocked off in case of expansion into the BU pod.
 
### IP space allocations

Every IP in the Anycast network must be contained in 10.128.0.0/9. 

  **10.192.1.0/30** (VLANS 2007,2008,2009,2010)
  * Every cache node is accessible to compute nodes at 10.192.1.1/30 on its individual Cache Node Vlan.
  * Each switch has interface on a distinct Cache Node Vlan with IP 10.192.1.2/30

  **10.224.1.0/24** (VLAN 2000)
  * IPs are assigned statically on the Cache Transit VLAN:

| IP | Assignment |
| ----------------- | ----------------------- |
| 10.224.1.1 | Ve 2000 Fabric Virtual Gateway |
| 10.224.1.7 | RBridge-7 Ve 2000 |
| 10.224.1.8 | RBridge-8 Ve 2000 |
| 10.224.1.9 | RBridge-9 Ve 2000 |
| 10.224.1.28 | RBridge-104 Ve 2000 \*\* |
| 10.224.1.10 | RBridge-10 Ve 2000 |
| 10.224.1.107 | Cache-c07 eth5.2000 \* |
| 10.224.1.108 | Cache-c08 eth5.2000 \* |
| 10.224.1.109 | Cache-c09 eth5.2000 \*
| 10.224.1.110 | Cache-c10 eth5.2000 \* |
| 10.224.1.254 | DHCP server |

*Note: the Cache server IPs on eth5.2000 are assigned statically via the DHCP server. See the [DHCP server config](#dhcp-setup) for more info. 

The last octet should be 100 + rack number of the cache server.  25-28 should be used as the 'rack number' of switches 101-104 respectively, if this becomes needed*

*IPs and VLANs ending in 25, 26, 27, and 28 on the various networks should be assigned to RBridges 101, 102, 103, and 104 respectively, because they are located in Row 4, Cabinets 2 and 4, and using the cabinet numbers would cause overlap with RBridges 2 and 4.

We do not expect to use these 4 switches in the main Anycast setup but they may be used for testing or hosting additional infrastructure.*

  **10.128.X.Y/23** (COMPUTE VLANS)
  * Each compute VLAN has an individual /23 IP space.  They are assigned in the following pattern:

| VLAN | IP Space |
| ----- | ---------------- |
| 2100 | 10.128.0.0/23 |
| 2101 | 10.128.2.0/23 |
| 2102 | 10.128.4.0/23 |
| 2103 | 10.128.6.0/23 |
| 2104 | 10.128.8.0/23 |
| 2105 | 10.128.10.0/23 |
| 2106 | 10.128.12.0/23 |
| 2107 | 10.128.14.0/23 |
| 2108 | 10.128.16.0/23 |
| 2109 | 10.128.18.0/23 |
| ... | ... |
| 2227 | 10.128.254.0/23 |
| 2228 | 10.129.0.0/23 |
| ... | ... |
| 2355 | 10.129.254.0/23 |
| 2356 | 10.130.0.0/23 |
| ... | ...
| 2483 | 10.130.254.0/23 |
| 2484 | 10.131.0.0/23 |
| ... | ... |
| 2611 | 10.131.254.0/23 |
| 2612 | 10.132.0.0/23 |
| ... | ... |
| 2699 | 10.132.174.0/23 |

On each of these VLANs, the first 32 IPs and the last 16 IPs are reserved.  Switches should always be assigned an IP of the following pattern:

  **10.X.Y.Z**  where the letters stand for the following:
  **X.Y**    The appropriate subnet for the VLAN (lower half)
  **Z**    The cabinet number of the switch (except for 101-104, which will use 25-28 respectively)

For example, **RBridge-7** has the following IPs:

  **VLAN 2100** : 10.128.0.7
     
  **VLAN 2101** : 10.128.2.7
  
  **VLAN 2102** : 10.128.4.7

...etc     

### Step-By-Step Configuration

 **Virtual fabric gateway configuration**

The mac address of the gateway is arbitrary, but should be a private mac address.

     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# router fabric-virtual-gateway
     RBridge-101(conf-router-fabric-virtual-gateway)# address-family ipv4
     RBridge-101(conf-address-family-ipv4)# no enable
     RBridge-101(conf-address-family-ipv4)# gateway-mac-address 0204.0204.0204
     RBridge-101(conf-address-family-ipv4)# gratuitous-arp timer 60
     RBridge-101(conf-address-family-ipv4)# accept-unicast-arp-request
     RBridge-101(conf-address-family-ipv4)# enable
     RBridge-101(conf-address-family-ipv4)# end
     RBridge-101#

  **Static Routes**

Each switch also needs a static route to a backup cache server. Static routes have an administrative distance of 1 by default, so they it will only be used if all direct connections are exhausted.

This means if the cache server fails, the relevant interface must be disabled on the directly connected switch before the static route can be used and failover can take place. 

The backup topology is currently:
  
     RBridge-7 ---> RBridge-9 ---> RBridge-10 ---> RBridge-8 ---> RBridge-7 

which was determined based on the physical topology of the switches.  As more switches are added to the setup, the backup topology should be altered.

The following configuration steps must be performed on each switch with a cache server directly connected. Config steps for two switches are shown.

     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# rbridge-id 7
     RBridge-101(config-rbridge-id-1)# ip route 0.0.0.0/0 10.224.1.104
     RBridge-101(config-rbridge-id-1)# ip route 10.192.1.0/30 10.224.1.9
     RBridge-101(config-rbridge-id-1)# top
     RBridge-101(config)# rbridge-id 8
     RBridge-101(config-rbridge-id-2)# ip route 0.0.0.0/0 10.224.100.104
     RBridge-101(config-rbridge-id-2)# ip route 10.192.1.0/30 10.224.1.7
     RBridge-101(config-rbridge-id-2)# end
    
If the compute vlan is expected to handle outgoing traffic, each switch also needs a default route to the edge router (assuming this network will handle outgoing traffic).

In that case, the static route supplied to the nodes by the dhcp server can be a default route to the edge router.

**Cache Transit Vlan**

The Cache Transit Vlan is on Vlan 2000.

First we create the Vlan :

     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# interface vlan 2000
     RBridge-101(config-Vlan-2000)# name anycast_Cache_Transit
     RBridge-101(config-Vlan-2000)# end
     RBridge-101#
     
Next, create a Virtual Ethernet Interface on each switch in the setup. The config below should be repeated for each switch, and each switch should have
its own IP (see the table above).

Config for switches 7 and 8 are shown in the example.
     
     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# rbridge-id 7
     RBridge-101(config-rbridge-id-7)# interface ve 2000
     RBridge-101(config-rbridge-Ve-2000)# ip address 10.224.1.7/24
     RBridge-101(config-rbridge-Ve-2000)# no shutdown
     RBridge-101(config-rbridge-Ve-2000)# top
     RBridge-101(config)# rbridge-id 8
     RBridge-101(config-rbridge-id-8)# interface ve 2000
     RBridge-101(config-rbridge-Ve-2000)# ip address 10.224.1.8/24
     RBridge-101(config-rbridge-Ve-2000)# no shutdown
     RBridge-101(config-rbridge-Ve-2000)# end
     RBridge-101#
     
Configure the Fabric Virtual Gateway interface. Attach all the switches which you created Ve 2000 in the previous step. 

In the example, switches 7 and 8 are attached.
     
     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# interface ve 2000
     RBridge-101(config-Ve-2000)# attach rbridge-id add 7
     RBridge-101(config-Ve-2000)# attach rbridge-id add 8
     RBridge-101(config-Ve-2000)# ip fabric-virtual-gateway
     RBridge-101(config-ip-fabric-virtual-gw)# gateway-address 10.224.1.1/24
     # see * below for omitted step here
     RBridge-101(config-ip-fabric-virtual-gw)# gratuitous-arp timer 15
     RBridge-101(config-ip-fabric-virtual-gw)# hold-time 30
     RBridge-101(config-ip-fabric-virtual-gw)# enable
     RBridge-101(config-ip-fabric-virtual-gw)# end
     RBridge-101#

The original instructions from Rob included this step here:
     
     RBridge-101(config-ip-fabric-virtual-gw)# load-balancing-disable

But this causes all traffic to get forwarded to the designated ARP router for the gateway, which in turn means that all traffic to the cache node IP ends up at the cache node under that router, which is definitely not desired behavior.

 **Cache Node VLANs**

A separate VLAN must be created for each cache node.  The VLAN number should be 2000 + cabinet number of the cache node.  This cabinet number is also used in the hostname of the cache node and as the RBridge-ID of the TOR switch.

So for example, cache-c07-01 is in cabinet 7, its TOR swich is RBridge-7, and its Cache Node VLAN is 2007.

In the example, cache transit vlans are created for cache nodes 7 and 8.

Create the VLANS:

     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# interface vlan 2007
     RBridge-101(config-Vlan-2007)# name anycast_Cache-C07
     RBridge-101(config-Vlan-2007)# top
     RBridge-101(config)# interface vlan 2008
     RBridge-101(config-Vlan-2008)# name anycast_Cache-C08
     RBridge-101(config-Vlan-2008)# end
     RBridge-101# 
     
     
Configure the Virtual Ethernet interfaces that connect to each VLAN. Notice that the IP is the same on every virtual interface.
     
     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# rbridge-id 7
     RBridge-101(config-rbridge-id-7)# interface ve 2007
     RBridge-101(config-rbridge-Ve-2007)# ip address 10.192.1.2/30
     RBridge-101(config-rbridge-Ve-2007)# no shutdown
     RBridge-101(config-rbridge-Ve-2007)# top
     RBridge-101(config)# rbridge-id 8
     RBridge-101(config-rbridge-id-8)# interface ve 2008
     RBridge-101(config-rbridge-Ve-2008)# ip address 10.192.1.2/30
     RBridge-101(config-rbridge-Ve-2008)# no shutdown
     RBridge-101(config-rbridge-Ve-2008)# end
     RBridge-101# 

  **Connect each Cache Server to the approriate VLANs**

For each Cache Node, the individual Cache Node VLAN and the Cache Transit VLAN should be added added to the relevant port on the switch. 

In the current setup the cache nodes have two 40Gb connections to the TOR switch.  The first, in port 51, is an access mode port reserved for Ceph traffic.  We should add the correct VLAN to the trunk port 52 on each switch.

This is shown for RBridges 7 and 8.

     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# interface fortygigabitethernet 7/0/52
     RBridge-101(conf-if-fo-7/0/52)# switchport
     RBridge-101(conf-if-fo-7/0/52)# switchport mode trunk
     RBridge-101(conf-if-fo-7/0/52)# switchport trunk allowed vlan add 2000
     RBridge-101(conf-if-fo-7/0/52)# switchport trunk allowed vlan add 2007
     RBridge-101(conf-if-fo-7/0/52)# no shutdown
     RBridge-101(conf-if-fo-7/0/52)# top
     RBridge-101(config)# interface fortygigabitethernet 8/0/52
     RBridge-101(conf-if-fo-8/0/52)# switchport
     RBridge-101(conf-if-fo-8/0/52)# switchport mode trunk
     RBridge-101(conf-if-fo-8/0/52)# switchport trunk allowed vlan add 2000
     RBridge-101(conf-if-fo-8/0/52)# switchport trunk allowed vlan add 2008
     RBridge-101(conf-if-fo-8/0/52)# no shutdown
     RBridge-101(conf-if-fo-8/0/52)# end
     RBridge-101#


  **Compute VLANs**

Each individual physical or virtual compute cluster will have its own VLAN.

We have reserved VLANs 2100-2699 for this purpose, but currently only VLANs 2100-2109 are configured.  The infrastructure cannot support more than about 600 simultaneous clusters.

The configuration is shown is for Vlan 2100 and 2101, but should be done for each compute vlan that is to be made available.

     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# interface vlan 2100
     RBridge-101(config-Vlan-2100)# name anycast_Compute_2100
     RBridge-101(config-Vlan-2100)# top
     RBridge-101(config)# interface vlan 2101
     RBridge-101(config-Vlan-2101)# name anycast_Compute_2101
     RBridge-101(config-Vlan-2101)# end
     RBridge-101#

Create the Virtual Ethernet Interfaces.  Each switch has a unique IP address on each interfacd.  These should be assigned in the pattern described in the IP Space section above.

The ip dhcp relay address points to a DHCP server which is configured to serve all the subnets used by compute vlans. 

These config steps should be performed on every switch. The example shows config for RBridges 7 and 8.

     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# rbridge-id 7
     RBridge-101(config-rbridge-id-7)# interface ve 2100
     RBridge-101(config-rbridge-Ve-2100)# ip address 10.128.0.7/23
     RBridge-101(config-rbridge-Ve-2100)# ip dhcp relay address 10.224.1.254
     RBridge-101(config-rbridge-Ve-2100)# no shutdown
     RBridge-101(config-rbridge-Ve-2100)# exit
     RBridge-101(config-rbridge-id-7)# interface ve 2101
     RBridge-101(config-rbridge-Ve-2101)# ip address 10.128.2.7/23
     RBridge-101(config-rbridge-Ve-2101)# ip dhcp relay address 10.224.1.254
     RBridge-101(config-rbridge-Ve-2101)# no shutdown
     RBridge-101(config-rbridge-Ve-2101)# top
     RBridge-101(config)# rbridge-id 8
     RBridge-101(config-rbridge-id-7)# interface ve 2100
     RBridge-101(config-rbridge-Ve-2100)# ip address 10.128.0.8/23
     RBridge-101(config-rbridge-Ve-2100)# ip dhcp relay address 10.224.1.254
     RBridge-101(config-rbridge-Ve-2100)# no shutdown
     RBridge-101(config-rbridge-Ve-2100)# exit
     RBridge-101(config-rbridge-id-2)# interface ve 2101
     RBridge-101(config-rbridge-Ve-2101)# ip address 10.128.2.8/23
     RBridge-101(config-rbridge-Ve-2101)# ip dhcp relay address 10.224.1.254
     RBridge-101(config-rbridge-Ve-2101)# no shutdown
     RBridge-101(config-rbridge-Ve-2101)# end
     RBridge-101#

Create the Fabric Virtual Gateway for the compute VLANs, and attach all switches.

     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# interface ve 2100
     RBridge-101(config-Ve-2100)# attach rbridge-id add 7
     RBridge-101(config-Ve-2100)# attach rbridge-id add 8
     RBridge-101(config-Ve-2100)# ip fabric-virtual-gateway
     RBridge-101(config-ip-fabric-virtual-gw)# gateway-address 10.128.0.1/23
     RBridge-101(config-ip-fabric-virtual-gw)# load-balancing-disable
     RBridge-101(config-ip-fabric-virtual-gw)# gratuitous-arp timer 15
     RBridge-101(config-ip-fabric-virtual-gw)# hold-time 30
     RBridge-101(config-ip-fabric-virtual-gw)# enable
     RBridge-101(config-ip-fabric-virtual-gw)# exit
     RBridge-101(config-Ve-2100)# interface ve 2101
     RBridge-101(config-Ve-2101)# attach rbridge-id add 7
     RBridge-101(config-Ve-2101)# attach rbridge-id add 8
     RBridge-101(config-Ve-2101)# ip fabric-virtual-gateway
     RBridge-101(config-ip-fabric-virtual-gw)# gateway-address 10.128.3.1/23
     RBridge-101(config-ip-fabric-virtual-gw)# load-balancing-disable
     RBridge-101(config-ip-fabric-virtual-gw)# gratuitous-arp timer 15
     RBridge-101(config-ip-fabric-virtual-gw)# hold-time 30
     RBridge-101(config-ip-fabric-virtual-gw)# enable
     RBridge-101(config-ip-fabric-virtual-gw)# end
     RBridge-101#

  **Connecting Compute Nodes to switches**

In general, this task should be handled by HIL.  However, this is the procedure if you are doing it manually for testing purposes. 

For these examples, assume 3 physical nodes connected to ports 1, 2, and 3 respectively on RBridge-7.

Physical node #1 will be part of a physical compute cluster using vlan 2100, and we wish this compute VLAN to be the native VLAN:

     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# interface tengigabitethernet 7/0/1
     RBridge-101(conf-if-te-7/0/1)# switchport
     RBridge-101(conf-if-te-7/0/1)# switchport mode trunk
     RBridge-101(conf-if-te-7/0/1)# switchport trunk native-vlan 2100
     RBridge-101(conf-if-te-7/0/1)# no switchport trunk tag native-vlan

     #(optional) if other vlans are needed:
     RBridge-101(conf-if-te-7/0/1)# switchport trunk allowed vlan add XXXX
     
     RBridge-101(conf-if-te-1/0/1)# no shutdown
     RBridge-101(conf-if-te-1/0/1)# end
     RBridge-101#


Physical node #2 will be part of a physical compute cluster on vlan 2101, but it won't be the native vlan:

     RBridge-101# configure terminal
     Entering configuration mode terminal 
     RBridge-101(config)# interface tengigabitethernet 7/0/2
     RBridge-101(conf-if-te-7/0/2)# switchport
     RBridge-101(conf-if-te-7/0/2)# switchport mode trunk
     RBridge-101(conf-if-te-7/0/2)# switchport trunk allowed vlan add 2101
     RBridge-101(conf-if-te-7/0/2)# no shutdown
     RBridge-101(conf-if-te-7/0/2)# end
     RBridge-101#

Physical node #3 will host virtual compute nodes which could be part of any cluster.  We attach all possible vlans (in this case, 2100-2109) to it. Appropriate bridging will need to be configured on the node itself, which will depend on the method being used to deploy virtual clusters.  That configuration is beyond the scope of this page.

     RBridge-101# configure terminal
     Entering configuration mode terminal
     RBridge-101(config)# interface tengigabitethernet 7/0/3
     RBridge-101(conf-if-te-7/0/3)# switchport
     RBridge-101(conf-if-te-7/0/3)# switchport mode trunk
     RBridge-101(conf-if-te-7/0/3)# switchport trunk allowed vlan add 2100-2109
     RBridge-101(conf-if-te-7/0/3)# no shutdown
     RBridge-101(conf-if-te-7/0/3)# end
     RBridge-101#

  **DHCP setup**

The DHCP server is running on moc-services and can be reached from moc-services at `10.13.66.14`.

It also has an IP of 10.224.1.254 on the Cache Transit VLAN.

It is configured to:
1. give static IPs to the cache servers on the Cache Transit VLAN.
2. give random IPs to phyisical or virtual compute nodes on the compute vlans.  
3. push a static route to the physical and virtual compute nodes which directs 10.128.0.0/9 traffic to the gateway of the compute vlan, where it can be routed to cache servers as appropriate.

Currently, VLANS 2100-2109 are configured for DHCP. As more VLANs are added to the cluster, they will need to be added to `/etc/dhcpd.conf`.
     
     # In/etc/dhcp/dhcpd.conf
     #
     # DHCP Server Configuration file.
     #   see /usr/share/doc/dhcp*/dhcpd.conf.example
     #   see dhcpd.conf(5) man page
     #
     
     log-facility local7;
     
     # Enable classless static routes
     option rfc3442-classless-static-routes code 121 = array of integer 8;
     option ms-classless-static-routes code 249 = array of integer 8;
     
     ## CACHE TRANSIT VLAN (2000)
     ## network 10.224.1.0/24
     
     # No random DHCP on this network
     subnet 10.224.1.0 netmask 255.255.255.0 {
     }
     
     # static IPs for Cache Servers
     host cache-c07-01 { 
         option host-name "cache-c07-01";
         hardware ethernet 68:05:ca:33:ff:10; 
         fixed-address 10.224.1.107;
     }
     
     host cache-c08-01 { 
         option host-name "cache-c08-01";
         hardware ethernet 68:05:ca:33:fe:b0;
         fixed-address 10.224.1.108;
     }
     
     host cache-c09-01 { 
         option host-name "cache-c09-01";
         hardware ethernet 68:05:ca:34:04:90;
         fixed-address 10.224.1.109;
     }
     
     host cache-c10-01 {
         option host-name "cache-c10-01";
         hardware ethernet 68:05:ca:33:fe:d0;
         fixed-address 10.224.1.110;
     }
     
     
     ## COMPUTE VLAN 2100
     ## network 10.128.0.0/23
     
     subnet 10.128.0.0 netmask 255.255.254.0 {
         range 10.128.0.32 10.128.1.240;
         option subnet-mask 255.255.254.0;
        option broadcast-address 10.128.1.255;
         option rfc3442-classless-static-routes 9, 10, 128, 10, 128, 0, 1, 10, 128, 0, 1;
         #option routers 10.128.0.1;
     }
     
     ## COMPUTE VLAN 2101
     ## network 10.128.2.0/23
     subnet 10.128.2.0 netmask 255.255.254.0 {
         range 10.128.2.32 10.128.3.240;
         option subnet-mask 255.255.254.0;
         option broadcast-address 10.128.3.255;
         option rfc3442-classless-static-routes 9, 10, 128, 10, 128, 2, 1, 10, 128, 2, 1;
         #option routers 10.128.2.1;
     }
     
     ## COMPUTE VLAN 2102
     ## network 10.128.4.0/23
     subnet 10.128.4.0 netmask 255.255.254.0 {
         range 10.128.4.32 10.128.5.240;
         option subnet-mask 255.255.254.0;
         option broadcast-address 10.128.5.255;
         option rfc3442-classless-static-routes 9, 10, 128, 10, 128, 4, 1, 10, 128, 4, 1;
         #option routers 10.128.4.1;
     }
     
     ## COMPUTE VLAN 2103
     ## network 10.128.6.0/23
     subnet 10.128.6.0 netmask 255.255.254.0 {
         range 10.128.6.32 10.128.7.240;
         option subnet-mask 255.255.254.0;
         option broadcast-address 10.128.7.255;
         option rfc3442-classless-static-routes 9, 10, 128, 10, 128, 6, 1, 10, 128, 6, 1;
         #option routers 10.128.6.1;
     }
     ## COMPUTE VLAN 2104
     ## network 10.128.8.0/23
     subnet 10.128.8.0 netmask 255.255.254.0 {
         range 10.128.8.32 10.128.9.240;
         option subnet-mask 255.255.254.0;
         option broadcast-address 10.128.9.255;
         option rfc3442-classless-static-routes 9, 10, 128, 10, 128, 8, 1, 10, 128, 8, 1;
         #option routers 10.128.8.1;
     }
     
     ## COMPUTE VLAN 2105
     ## network 10.128.10.0/23
     subnet 10.128.10.0 netmask 255.255.254.0 {
         range 10.128.10.32 10.128.11.240;
         option subnet-mask 255.255.254.0;
         option broadcast-address 10.128.11.255;
         option rfc3442-classless-static-routes 9, 10, 128, 10, 128, 10, 10, 128, 10, 1;
     
     ## COMPUTE VLAN 2105
     ## network 10.128.10.0/23
     subnet 10.128.10.0 netmask 255.255.254.0 {
         range 10.128.10.32 10.128.11.240;
         option subnet-mask 255.255.254.0;
         option broadcast-address 10.128.11.255;
         option rfc3442-classless-static-routes 9, 10, 128, 10, 128, 10, 10, 128, 10, 1;
         #option routers 10.128.10.1;
     }
     
     ## COMPUTE VLAN 2106
     ## network 10.128.12.0/23
     subnet 10.128.12.0 netmask 255.255.254.0 {
         range 10.128.12.32 10.128.13.240;
         option subnet-mask 255.255.254.0;
         option broadcast-address 10.128.13.255;
         option rfc3442-classless-static-routes 9, 10, 128, 10, 128, 12, 1, 10, 128, 12, 1;
         #option routers 10.128.13.1;
     }
     
     ## COMPUTE VLAN 2107
     ## network 10.128.14.0/23
     subnet 10.128.14.0 netmask 255.255.254.0 {
         range 10.128.14.32 10.128.15.240;
         option subnet-mask 255.255.254.0;
         option broadcast-address 10.128.15.255;
         option rfc3442-classless-static-routes 9, 10, 128, 10, 128, 14, 1, 10, 128, 14, 1;
         #option routers 10.128.14.1;
     }
     
     ## COMPUTE VLAN 2108
     ## network 10.128.16.0/23
     subnet 10.128.16.0 netmask 255.255.254.0 {
         range 10.128.16.32 10.128.17.240;
         option subnet-mask 255.255.254.0;
         option broadcast-address 10.128.17.255;
         option rfc3442-classless-static-routes 9, 10, 128, 10, 128, 16, 1, 10, 128, 16, 1;
         #option routers 10.128.4.1;
     }
     
     ## COMPUTE VLAN 2109
     ## network 10.128.18.0/23
     subnet 10.128.18.0 netmask 255.255.254.0 {
         range 10.128.18.32 10.128.19.240;
         option subnet-mask 255.255.254.0;
         option broadcast-address 10.128.19.255;
         option rfc3442-classless-static-routes 9, 10, 128, 10, 128, 18, 1, 10, 128, 18, 1;
         #option routers 10.128.18.1;
     }

