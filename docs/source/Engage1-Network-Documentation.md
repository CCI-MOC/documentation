#### Hardware and Networking Layout
Where is everything, and what is connected to what ?
These spreadsheets will tell you:  
[Engage1_networks.xlsx](inventory_xlsx/Engage1_networks.xlsx)     
[Engage1_racks_and_hardware.xlsx](inventory_xlsx/Engage1_racks_and_hardware.xlsx)     
[Engage1_Brocade_cable_map.xlsx](inventory_xlsx/Engage1_Brocade_cable_map.xlsx)     

New management cable map can be found here: [[Engage1 Management Cable Map]]   
(Old document is [here](inventory_xlsx/Engage1_1G_cable_map.xlsx) just in case, but is completely outdated.)

Following is the snapshot of what the rack layout is as of March 2015 
![](MGHPCCRackLocation.png)

[MGHPCCRackAssignments032015.pptx]( MGHPCCRackAssignments032015.pptx)

Rough diagram of the Ceph storage: 
   
![](engage1_ceph.png)

Documentation of PDU configuration etc\:   
[[PDU Documentation]]

*****
### VLANs
 
| VLAN ID | Used for 
| --- | ---
| 10 | Public Internet via CSAIL (old) 
| 11 | ?
| 105 | ?
| 1602 | MRI Provisioning vlan
| 2000-2099 | *reserved for Anycast Setup*
| 2000 | Anycast Cache Transit
| 2007 | Anycast Cache Node 7
| 2008 | Anycast Cache Node 8
| 2009 | Anycast Cache Node 9
| 2010 | Anycast Cache Node 10
| 2100-2699 | *reserved for Anycast Computes*
| 2100-2109 | Anycast compute vlans actually configured 
| 3000-3099 | HIL vlans
| 3799 | BMI/BMI dev openstack floating IPs 172.31.0.0/22
| 3800-3850 | *reserved for MeetMe uses*
| 3800 | MeetMe
| 3801 | MeetMe
| 4001 | ?
| 4003 | OpenFlow Vlan 1
| 4004 | Foreman / Provisioning
| 4005 | OpenFlow Vlan 2
| 4025 | ?
| 4050 | Ceph
| 4051 | ?HAAS?
| 4052 | Engage1 openstack private 192.168.128.0/22
| 4053 | Engage1 BMI openstack private 192.168.132.0/22
| 4054 | Engage1 BMI dev openstack private 192.168.136.0/22
| 4060-4069 | Previously used by MRI nodes, should be replaced with 2100-2109
| 4080 | ?

### IP addresses

##### Public (via CSAIL)
     VLAN 10 - infrastructure, DHCP
     128.52.60.97      engage1-emergency (enp4s0f0)
     128.52.62.147     moc-services (br10,eth0.10)
     128.31.20.0/22    VLAN 3801**

**see details [below](#vlan-3801)

##### VLAN 3801
floating IPs, infrastructure with direct connection to Kai/Kumo
128.31.20.0/22
     
     # infrastructure
     128.31.20.1     to 128.31.20.10 are reserved by CSAIL for network uses
     128.31.20.11    to 128.31.20.14 are reserved by MOC 
     128.31.20.15    e1-radosgw-01  (VM on engage1-emergency)

     # OpenStack floating IPs
     128.31.22.0 to 128.31.23.254 

##### Anycast (VLANS 2000-2699) 
See [[Engage1 Anycast Setup]]
##### OBM (VLAN 3040)
Includes OBM network for servers in MOC racks, as well as the cache servers in MIT's rack, via the Dell Switch in r5-pA-c1. Previously the two switches were on different subnets, but these have been merged into a single 10.10.10.0/24 network as of 22 Jan 2016.

     (.1 to .15 Network Reserved)

          10.10.10.1   e1-r4pAc04-mgmt (Cisco),admin, cisco, wittinglypreconfigure
          10.10.10.2   e1-r4pAc02-mgmt (Cisco),admin, cisco, wittinglypreconfigure
          10.10.10.3   kumo switch
          10.10.10.4   e1-cacti
          10.10.10.5   e1-r4pAc04-mgmt-02 (Juniper)  (credentials: root/admin38)
          
                      
See [Kumo documentation](https://github.com/CCI-MOC/moc/wiki/Kumo-Network-Documentation) for Kumo switches

          10.10.10.15  moc-haas01 - network managing port


     # OpenStack Kilo servers (.16 to .47)

          10.10.10.16    moc-haas01 - IPMI port        
          10.10.10.17    moc-services01    
          10.10.10.18    moc-sdn01
          10.10.10.19    moc-controller01
          10.10.10.20    moc-controller02
          10.10.10.21    moc-compute03
          10.10.10.22    moc-emergency
          10.10.10.23    e1-compute-04 (openstack compute)
          10.10.10.24    e1-compute-05 (openstack compute)
          10.10.10.25    e1-compute-06 (openstack compute)
          10.10.10.26    e1-compute-07 (openstack compute)
          10.10.10.27    e1-compute-08 (openstack compute)
          10.10.10.28    e1-compute-09 (openstack compute)
          10.10.10.29    e1-vmhost-10 (staging kvm host)
          10.10.10.30    e1-bmi-11 (bmi)
          10.10.10.31    e1-bmi-12 (bmi)
          10.10.10.32    intel server 
          10.10.10.33    intel server 
                 
          10.10.10.34   currently used by R4-PA-C4 PDUL
          10.10.10.35   currently used by R4-PA-C4 PDUR

          10.10.10.36    intel server 
          10.10.10.37    intel server

          10.10.10.45    e1-gcompute-10 (dell r730)
          10.10.10.46    e1-gpu2 (dell r730)
          10.10.10.47    e1-gpu3 (dell r730)


      # PDUS (.56 to .63)
          Note - from the hot aisle, PDU A is on the right and PDU B on the left

          10.10.10.56   reserved for R4-PA-C2 PDU A
          10.10.10.57   reserved for R4-PA-C2 PDU B 
          10.10.10.58   reserved for R4-PA-C4 PDU A 
          10.10.10.59   reserved for R4-PA-C4 PDU B
          10.10.10.60   reserved for R4-PA-C23 PDU A
          10.10.10.61   reserved for R4-PA-C23 PDU B
          10.10.10.62   --- available ---
          10.10.10.63   --- available ---

     # Brocade Switches (.64 to .95)
          
          10.10.10.64    RBridge-101 (A1)
          10.10.10.65    RBridge-102 (B1)
          10.10.10.66    RBridge-103 (C1)
          10.10.10.67    RBridge-104 (D6)
          10.10.10.68    RBridge-2 (D5)
          10.10.10.69    RBridge-3 (A2)
          10.10.10.70    RBridge-4 (D4)
          10.10.10.71	 reserved for RBridge-5 (A6)
          10.10.10.72    RBridge-6 (D3)
          10.10.10.73    RBridge-7 (A3)
          10.10.10.74    RBridge-8 (D2)
          10.10.10.75    RBridge-9 (A5)
          10.10.10.76    RBridge-10 (D1)
          10.10.10.77    RBridge-11 (A4)
          10.10.10.78    RBridge-13 (C2)
          10.10.10.79    RBridge-14 (C5)
          10.10.10.80    RBridge-15 (C3)
          10.10.10.81    RBridge-16 (C4)
          10.10.10.82    RBridge-17 (B2)
          10.10.10.83    RBridge-19 (B5)
          10.10.10.84    RBridge-21 (B3)
          10.10.10.85    RBridge-23 (B4)

          10.10.10.90    OpenFlow switch (r4pAc04)

      # Ceph Storage  (.96 to .119)
          
          10.10.10.101     ceph-lenovo01      
          10.10.10.102     ceph-lenovo02
          10.10.10.103     ceph-lenovo03
          10.10.10.104     ceph-lenovo04
          10.10.10.105     ceph-lenovo05
          10.10.10.106     ceph-lenovo06
          10.10.10.107     ceph-lenovo07
          10.10.10.108     ceph-lenovo08
          10.10.10.109     ceph-lenovo09
          10.10.10.110     ceph-lenovo10
          10.10.10.111     ceph-quanta01
          10.10.10.112     ceph-quanta02
          10.10.10.113     ceph-quanta03
     
     # Admin Nodes  (.120 to .127)

          10.10.10.124    moc-services01 (in-band access interface)
          10.10.10.126    engage1-emergency (in-band access interface)

     # Cache Servers  (.172 to 191)

          10.10.10.172    reserved for C2 cache server
          10.10.10.173    reserved for C2 cache server
          10.10.10.174    reserved for C2 cache server
          10.10.10.175    reserved for C5 cache server (still in use by Chris Hill)
          10.10.10.176    reserved for C6 cache server
          10.10.10.177    cache-c7-01
          10.10.10.178    cache-c8-01
          10.10.10.179    cache-c9-01
          10.10.10.180    cache-c10-01
          10.10.10.181    cache-c13-01    <--- eventually this will be switched to c11 cache server
          10.10.10.182    (reserved for ? just to keep the pattern)
          10.10.10.183    reserved for C13 cache server
          10.10.10.184    reserved for C14 cache server
          10.10.10.185    reserved for C15 cache server
          10.10.10.186    reserved for C16 cache server
          10.10.10.187    reserved for C17 cache server  <-- note that the pattern breaks down after this
          10.10.10.188    reserved for C19 cache server 
          10.10.10.189    reserved for C21 cache server
          10.10.10.190    reserved for C23 cache server
          10.10.10.191    cache-104-01 (in row 4 pod b c04)

    (.192-.199 Available)
          
         10.10.10.{200-254} reserved for R4-PA-C23 deployment* 


*see [[R4-PA-C23 Documentation]]

##### OpenFlow DMZ (VLAN 4000)
Various virtual machines and switches for the OpenFlow researchers

          10.0.10.1   e1-services
          10.0.10.2   --*reserved for moc-sdn01h (hardware host)*--
          10.0.10.3   e1-sdn01 (virtual machine on moc-sdn01h)
          10.0.10.4   mininet-dhcp (virtual machine on moc-sdn01h)
          10.0.10.5   (formerly e1-compute-11)
          10.0.10.6   (formerly e1-compute-12)
          ...
          10.0.10.11  e1-openflow-01 (vm on e1-vmhost-10)
          10.0.10.12  e1-openflow-02 (vm on e1-vmhost-10)
          10.0.10.16  e4-r4pAc04-oflow01 management

##### OpenFlow VLANs (VLAN 4003 and 4005)
These are used to force packets traveling between the openflow research VMs to pass through the OpenFLow switch

          #4003
          192.168.156.1      moc-sdn01h br0 via enp2s0f1
          192.168.156.11     e1-openflow-01
            
          #4005
          192.168.156.1      moc-sdn01h br0 via enp2s0f0.4005
          192.168.156.12     e1-openflow-02

##### Ceph Client Network (VLAN 4050)  192.168.64.0/22
    
     192.168.64.1       moc-services
     192.168.64.2       redhatvm
     192.168.64.4       moc-sdn01h
     192.168.64.5       moc-haas01
     192.168.64.6       e1-compute-07 (openshift node)
     192.168.64.11      ceph-lenovo01
     192.168.64.12      ceph-lenovo02
     192.168.64.13      ceph-lenovo03
     192.168.64.14      ceph-lenovo04
     192.168.64.15      ceph-lenovo05
     192.168.64.16      ceph-lenovo06
     192.168.64.17      ceph-lenovo07
     192.168.64.18      ceph-lenovo08
     192.168.64.19      ceph-lenovo09
     192.168.64.20      ceph-lenovo10
     192.168.64.100     ?
     192.168.64.111     kumo-emergency 
     192.168.64.112     kumo-services
     192.168.64.126     engage1-emergency
     192.168.64.201     ceph-quanta01
     192.168.64.202     ceph-quanta02
     192.168.64.203     ceph-quanta03
     192.168.65.111     kumo-storage01
     192.168.66.1       e1-control-01
     192.168.66.2       e1-control-02
     192.168.66.101     e1-vcontrol-101
     192.168.66.121     Ceilometer (vm on e1-vhost-06)
     192.168.66.122     Openshift_Client (vm on e1-vhost-06)
     192.168.66.161     e1-mri-control-161
     192.168.66.195     e1-mri-compute-195
     192.168.67.7       cache-c07-01
     192.168.67.8       cache-c08-01
     192.168.67.9       cache-c09-01
     192.168.67.10      cache-c10-01
     192.168.67.11      e1-radosgw-01 (VM on e1-emergency)

*****

#### IPMI Access
IPMI can be accessed from either moc-services or engage1-emergency.  It is recommended to use moc-services because you can use X forwarding to get a GUI.

        [you@your-local-box]$ ssh 128.52.62.147 -X -Y
        [you@moc-services]$ firefox

Firefox should open, assuming you have appropriate X forwarding software installed on your local machine.  For a Mac, you need to install XQuartz.

Connecting via engage1-emergency requires some other solution to get a GUI.  If you have a [[dedicated VM|VM-Setup-for-Cisco-IPMI-Access]] set up for IPMI access with redsocks proxy utility, you can use that.  

You can also configure the browser on your local machine to use a SOCKS proxy.  You may need to install a Java web plugin and/or browser flash plugin in order to use the IPMI web GUI.  Depending on your system and configuration, some of the advanced functionality (like mounting a remote disk via IPMI) might not work in this setup.

First, connect to the emergency-box, using the port you configured in your SOCKS proxy - in this example, it is port 9000:

        [you@your-local-box]$ ssh -D 9000 128.52.60.97
        [you@engage1-emergency]$

Next, open a browser window, and navigate to the appropriate IP for the server you want.


##### Dell Switch
MRI Dell Switch (for cache server IPMI) - located in r5-pA-c1.  It is accessed via an MIT gateway, so please talk to Laura, Rado, or Rahul if you need something configured there.

Instructions for how to log in are here: [[Accessing the MRI Dell Switch]].

##### Login credentials:  
(These are likely to change away from the defaults in the near future.)

Quanta QSSC-S99-1U   
(all 6 Kilo servers, 3 ceph-quanta servers, engage1-emergency)   
Default credentials: root/root
Default IPMI address: 192.168.001.002

Lenovo Servers:  
(10 ceph-lenovo servers)  
Default credentials: USERID/PASSW0RD
Default IPMI address: 192.168.70.125

BU Intel servers:
(cache-c104, e1-compute-08, e1-compute-09, e1-compute-10)
NO DEFAULT CREDENTIALS OR IP - must be set in BIOS
Currently set to: 
root/P@ssw0rd1

Cache servers:   
(6 Intel servers)  
Default credential: ADMIN/ADMIN
Video:  http://www.screencast.com/t/gazhGb8H

Brocade Switches:   
(22 Brocade VDX 6740)  
Credentials: admin/brocade123  
Default credentials: admin/password   
Default console IP: none, set to DHCP

#### Temporary Connections:

Because I didn't have 10G cables or connectors, I connected one 1G NIC on the
each of the new dell nodes to cisco-04 management switch. dell-45 = port 21; dell-46 = port
22; dell-47 = port 23.
 
