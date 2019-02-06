1.Add a new vlan id, for example 300

    snmpset -v1 -cadmin 192.168.0.1 1.3.6.1.4.1.11863.1.1.4.3.1.1.2.1.1.300 i 300

1.3.6.1.4.1.11863.1.1.4.3.1.1.2.1.1 is the OID for portVlanId

2.Set the new vlan id status as "createAndGo(4)",initially it is notReady, and won't allow you to add ports to it. 

    snmpset -v1 -cadmin 192.168.0.1 1.3.6.1.4.1.11863.1.1.4.3.1.1.2.1.6.300 i 4

1.3.6.1.4.1.11863.1.1.4.3.1.1.2.1.6 is the OID  for portVlanStatus
The values are as follows
active(1), notInService(2), notReady(3), createAndGo(4), createAndWait(5), destroy(6)

So, similarly

    snmpset -v1 -cadmin 192.168.0.1 1.3.6.1.4.1.11863.1.1.4.3.1.1.2.1.6.300 i 6

will remove vlan 300.

3.Add some untagPort to vlan

    snmpset -v1 -cadmin 192.168.0.1 1.3.6.1.4.1.11863.1.1.4.3.1.1.2.1.4.300 s "1,3,5"

1.3.6.1.4.1.11863.1.1.4.3.1.1.2.1.4 is the OID for vlanUntagPortMemberAdd

Now we have a new vlan 300 with port 1,3,5 good to go. 

Note: Mibble is a tool to parse MIB files.