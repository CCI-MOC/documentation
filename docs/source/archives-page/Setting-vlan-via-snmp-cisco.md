http://www.cisco.com/en/US/tech/tk648/tk362/technologies_tech_note09186a00801c6035.shtml

These steps are well documented, but some details still required trial and error. It is not exactly the same (neither the OID nor the pattern) as TP-Link.

Set the vtpVlanEditOperation to the copy state (integer 2). This allows you to create the VLAN.

    snmpset -v2c -c private 192.168.0.254 vtpVlanEditOperation.1 i 2

This example is VLAN 7, create a row and set the type and name.

    snmpset -v2c -c private 192.168.0.254 vtpVlanEditRowStatus.1.7 i 4
    snmpset -v2c -c private 192.168.0.254 vtpVlanEditType.1.7 i 1
    snmpset -v2c -c private 192.168.0.254 vtpVlanEditName.1.7 s "vlan_7"

Set the vtpVlanEditDot10Said. This is the VLAN number + 100000 translated to hexadecimal. This example creates VLAN 7, so the vtpVlanEditDot10Said should be: 11 + 100000 = 100007 -> Hex: 000186A7

    snmpset -v2c -c private 192.168.0.254 vtpVlanEditDot10Said.1.7 x 000186A7

When you have created VLAN 7, you must apply the modifications.

    snmpset -v2c -c private 192.168.0.254 vtpVlanEditOperation.1 i 3

One step instruction to set vlan 6.

    snmpset -v 2c -c private 192.168.0.254 1.3.6.1.4.1.9.9.46.1.4.1.1.1.1 i 2 1.3.6.1.4.1.9.9.46.1.4.1.1.3.1 s "mocuser" 
    snmpset -v 2c -c private 192.168.0.254 1.3.6.1.4.1.9.9.46.1.4.2.1.11.1.6 i 4 1.3.6.1.4.1.9.9.46.1.4.2.1.3.1.6 i 1 1.3.6.1.4.1.9.9.46.1.4.2.1.4.1.6 s "vlan6" 1.3.6.1.4.1.9.9.46.1.4.2.1.6.1.6 x 000186A6 1.3.6.1.4.1.9.9.46.1.4.1.1.1.1 i 3
    snmpset -v 2c -c private 192.168.0.254 1.3.6.1.4.1.9.9.46.1.4.1.1.1.1 i 4
    snmpwalk -v 2c -c public 192.168.0.254 1.3.6.1.4.1.9.9.46.1.3.1.1.2
