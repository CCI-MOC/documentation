[2015/02/10]
compute14 b8 2A 72 4F A3 FB

[2015/01/30] - **Jon/Ian/Huy**

Talked to #puppet-openstack, they suggested iscsi cinder backend, which uses lvm. We found the appropriate variable in quickstack/params.pp, need to make sure everything around cinder is set correctly and set up lvm.

We're still trying to pin down what to use for swift. 

We were having networking issues, tried to put node 12 (re-assigned to compute) back on production cluster to refresh puppet config on the machine, had to put compute12 back on vlan 2443 to talk to the puppet environment.

Need to figure out how to attach the dev environment nodes to pxe from satellite on vlan 2442. Both the nodes and moc-sat have connectivity for the vlan, have to figure out where in satellite to specify vlan for pxe booting.

# Diagram

![](01-30-15 Diagram of Switch to Machines.png)