# Nexus 5672 Switch

### CLI access
The nexus switches can be accessed via ssh, username/password: see bitwarden Kaizen Nexus 5672 Switch.

Each of the two switches has the following Access List rules which only allows access from the two HaaS masters and the Emergency box set:

    ip access-list inBandAccessList
      statistics per-entry
      100 permit ip 129.10.25.0/24 any
      200 permit ip 10.0.0.0/8 any
      300 permit ip 192.168.24.0/24 any
      400 permit ip 129.10.3.0/24 any
      1000 deny ip any any log

    line vty
      access-class inBandAccessList in

### Disabling STP on host nodes
For preventing PXE/DHCP timeouts

By default, the switches have Spanning Tree Protocol enabled on ports, causing a delay when cycling the port that can cause PXE to timeout.

To disable STP, run the following commands:
```
configure
interface 1/<port number>
spanning-tree port type edge trunk
^Z
```

