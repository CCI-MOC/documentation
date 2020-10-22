# MGHPCC Power On After Shutdown

1. Power on the ipmi gateways and the vpn server.
1. Turn on foreman and any other DHCP server so that hosts that depend on those have their networking set correctly.
1. Turn on all ceph OSD servers and monitors together.
1. Once ceph is up and running, proceed to start openstack and any other services that depend on the ceph cluster.
1. If a power9 node's BMC/IPMI controller is unreachable, then it is possible that the BMC lost all config and defaults to dhcp for IPMI IP. In that case, look at the dnsmasq lease file to on kzn-ipmi-gw to see if an IP was assigned on the `10.0.0.0/19` network. 
