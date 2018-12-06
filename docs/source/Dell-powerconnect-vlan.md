http://www.dell.com/downloads/global/products/pwcnt/en/app_note_8.pdf
Download the MIBs files here.
http://en.community.dell.com/support-forums/servers/f/866/t/17707633.aspx

Put them in the folder

    ~/.snmp/mibs

In .snmp/snmp.conf
Add 

    mibs +ALL

The following creates vlan 108 with name "vlan108" on the switch.

    snmpset -v1 -cDell_Network_Manager 192.168.3.245 \
    dot1qVlanStaticName.108 s "vlan108" \
    dot1qVlanStaticEgressPorts.108  x        '00' \
    dot1qVlanForbiddenEgressPorts.108 x  '00' \
    dot1qVlanStaticUntaggedPorts.108  x   '00' \
    dot1qVlanStaticRowStatus.108         i  4

