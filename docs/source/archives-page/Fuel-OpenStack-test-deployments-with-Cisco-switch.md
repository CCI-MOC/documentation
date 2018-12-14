# Fuel-OpenStack Test Deployments with Cisco Switch

Fuel Master is at 10.20.0.2, web interface at `http://10.20.0.2:8000`.

This network is accessible via Will's workstation or my workstation (which both have a NIC hooked directly into the PXE vlan, which is vlan #2 as described below.)

On the Fuel Web GUI, you can see how the HA Compact installation is set up

Home-->Environments-->HA Compact single-ethernet

Network Settings tab

Choose VLAN Manager

### Cluster 1 network setup

**public**
* 192.168.3.129-150
* vlan tag 100
* netmask 255.255.255.0
* gateway 192.168.3.250


**floating**
* 192.168.3.151-200
* vlan tag 100

**mgmt**
* 172.18.0.0./24
* vlan tag 101

**storage**
* 172.16.0.0/24
* vlan tag 102

**vm networks (fixed)**
* 10.0.0.0/18
* networks 64
* size 256
* vlan id range start 103 end 166

### Cluster 2
Cluster 2 is running FlatDHCP and is configured as follows 

**Public** 
* 192.168.3.2-64 (VLAN 100)
* netmask 255.255.255.0
* gateway 192.168.3.250

**Floating**
* 192.168.3.65-128 (VLAN 100)
* Management: 172.19.0.0/24 (VLAN 201)
* Storage: 172.19.1.0/24 (VLAN 202)
* VM Networks (Fixed): 172.19.2.0/24 (VLAN 203)

The Cisco switch is currently tethered directly to Will's workstation at 192.168.3.251.

`screen /dev/ttyS0` and hit enter a couple of times to open it.

All of the VLANs needed by the VM networks are set up here. You can see them with `show vlan`.

*Remember, Cisco IOS can use shorthand, so you'll see me writing a lot of it -- here, I just type "sh vlan".

To see the full expansions of things, use the "?" key.*
```
Switch>sh vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Gi1/0/7, Gi1/0/8, Gi1/0/9
                                                Gi1/0/10, Gi1/0/11, Gi1/0/12
                                                Gi1/0/27, Gi1/0/28
2    PXE                              active    Gi1/0/1, Gi1/0/2, Gi1/0/6
                                                Gi1/0/13, Gi1/0/14, Gi1/0/15
                                                Gi1/0/16, Gi1/0/17
3    HA                               active
100  Public/Floating                  active    Gi1/0/18, Gi1/0/19, Gi1/0/20
                                                Gi1/0/21, Gi1/0/22, Gi1/0/23
                                                Gi1/0/24, Gi1/0/25, Gi1/0/26
101  Management                       active
102  Storage                          active
103  VLAN0103                         active
104  VLAN0104                         active
105  VLAN0105                         active
106  VLAN0106                         active
107  VLAN0107                         active
108  VLAN0108                         active
109  VLAN0109                         active
110  VLAN0110                         active
```

...and so on for the rest of the VLANs needed.

You'll see ports 1, 2, and 6 are on VLAN2, for the PXE network. These are ports on Will's machine, my machine, and the Fuel master.

To do this, we did:
```
enable
configure terminal
int gi1/0/1
switchport mode access
sw ac vlan 2
```
For the dual-ethernet-card machines without VLAN tagging, we used the same set of commands to set the switchport access for their private PXE cards (ports 13-17) to vlan 2, and their public access cards (ports 18-21) to VLAN 100.

To set the ports for the single-ethernet-card machines (for the HA-Compact Single Ethernet deployment), let's demonstrate by adding ports 7 and 8 (the two new machines I added to reprovision Will's Multi-Node deployment into an HA-Compact deployment).

Note that we'll use "interface range" to set both ports identically but only have to set them once.

We have to actually enter the VLAN database before we make the VLANs on a Cisco:
```
vlan database
vlan 101 name Management
```

We do not have to do this on a Dell PowerConnect switch -- on the Dell, they get made simply as "interfaces".

If you do this on a Cisco, it will not actually create the VLAN tag entries in the VLAN database.
```
interface vlan 101
name Management
```
See [this example](http://www.cisco.com/en/US/tech/tk389/tk689/technologies_configuration_example09186a008009478e.shtml#confthevlancat)

Now we can add the VLAN tags to the switchports
```
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#int range gi1/0/7-8
Switch(config-if-range)#sw trunk allowed vlan 2,100-166
Switch(config-if-range)#sw trunk native vlan 2
Switch(config-if-range)#sw trunk pruning vlan 2,100-166
Switch(config-if-range)#sw trunk encap dot1q
Switch(config-if-range)#sw mo trunk
Switch(config-if-range)#exit
Switch(config)#exit
Switch#
```

We can show what the switchport configuration on the interface looks like:
```
Switch>sh int gi1/0/8 sw
Name: Gi1/0/8
Switchport: Enabled
Administrative Mode: trunk
Operational Mode: trunk
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: dot1q
Negotiation of Trunking: On
Access Mode VLAN: 1 (default)
Trunking Native Mode VLAN: 2 (PXE)
Administrative Native VLAN tagging: enabled
Voice VLAN: none
Administrative private-vlan host-association: none 
Administrative private-vlan mapping: none 
Administrative private-vlan trunk native VLAN: none
Administrative private-vlan trunk Native VLAN tagging: enabled
Administrative private-vlan trunk encapsulation: dot1q
Administrative private-vlan trunk normal VLANs: none
Administrative private-vlan trunk private VLANs: none
Operational private-vlan: none
Trunking VLANs Enabled: 2,100-166
Pruning VLANs Enabled: 2,100-166
Capture Mode Disabled
Capture VLANs Allowed: ALL
          
Protected: false
Unknown unicast blocked: disabled
Unknown multicast blocked: disabled
Appliance trust: none
```

Now our two new machines have their networking set up identically to our existing machines.  We can test things now.

In this case, I had set up the HA Compact single-ethernet configuration with the three existing machines as redundant controllers, so I added the two more as a compute node and a cinder.

Once you're satisfied that everything is working properly, you should save the configuration on the switch
```
Switch#copy running-config startup-config
Destination filename [startup-config]?
Building configuration...
[OK]
```

