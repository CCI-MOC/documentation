# BU CCDS Networks

2 racks have been allocated to the MOC in the room SB05-A. The racks are labelled 665-PA-F1 and 665-PA-R1.

BU is using Cisco Application Policy Infrastructure Controller (APIC) to manage the switches. There's a tenant called
"MassOpenCloud" which holds configuration for our racks.

## Public BU (VLAN 122)

This VLAN is enabled on ports 1-4 of both TOR cisco switches.

Cisco APIC Application Profile: `moc-public`

- Subnet: 128.197.248.8/29

```
IP Address       Hostname/Description
128.197.248.9      Gateway
128.197.248.10     ccds-intel-rh.massopen.cloud
128.197.248.11     arm-server-rh.massopen.cloud
128.197.248.12     fedora-riscv (Ahmed/Isaiah)
```

Note that 128.197.248.15 is the broadcast address for this subnet so that's not usable.


## Private BU (VLAN TBD)

This VLAN will be enabled on ports 5-45 of both TOR cisco switches.

Cisco APIC Application Profile: `moc-private`

- Subnet: TBD
