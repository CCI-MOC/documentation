# Network Topology

Logical View

<!--![](https://github.com/CCI-MOC/papers/blob/master/engage1/Engage1Network.png)-->`

There are 18 Compute Racks which have 18 Servers and three of them have Cache Server. Each Server within Rack is connected to Switch via 10GB link, and each Cache server within Rack is connected to Switch via 2x 40GB link.

There is one Openstack Rack with 3 servers, and these servers are used for Openstack Experiments.

There are 3 CEPH Storage Racks. Each switch is connected to other two switches via 4x10 GB link. Each storage server is connected to 2 different switches in storage racks via 10GB link.

