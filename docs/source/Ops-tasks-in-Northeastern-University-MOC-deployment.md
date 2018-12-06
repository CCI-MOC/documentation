
List (in priority order) of Ops tasks as of 9/15/2015 

1. Write a fresh configuration for the 2 Nexus switches. This configuration should disable STP for most ports (for instance, "spanning-tree port type edge trunk"), then explicitly enable STP for the switch-to-switch ports (the 2 40GB interfaces and the uplink(s)). If we could create a lower-privileged user for HaaS that can manage a particular set of ports while not being able to do nasty stuff like make firmware changes. 

1. FW upgrade on all 42 nodes. Rajiv did one upgrade already - node 33.

1. Node 5 was disconnected as it generated unusual network traffic that ended up causeing network packets back log. Rajiv mentioned it's likely to be bad hardware. Need to follow up/track to make sure it's fixed and back up again

1. Node 7 and node 11 (double check with Ravi/Apoorve) are not being used as the device partition that works for other nodes do not work for these two. Brent (RH) provided us with this device partiton.

1. Replace 2 1G management switches. The new ones had been delivered to Anand's office on 9/11. The ones that are in the deployment were on loan from NU networking IT.

1. Monitoring issues with physical infrastructure.

1. Bad networking card on Cisco Node 5 ? The node was taken offline for now until MOC get green light from NU

1. From notes taken during Security meeting between MOC and NU on Aug. 28 1:30 - 3:30 pm.
* logging how ? (Rajiv) ing and servers ; use QRedar from IBM ; for both physical and virtual
* Tiger team analysis (Rajiv) do the FW upgrade; taking the bad node off and let the tiger team check the image for further analysis
* FW upgrade for switches. Get notification and decide. March 2015 version of FW ; should be ok (Anand) - no security update, we are fine with current version
* BGP opened to the world. BGP TTL list, TTL of 1 "should not" impact much. But Anand will turn it off
* possibility of running netflow to give src/dst of packets (Anand)
* MOC subnets dont go through IPS; We want the emergency box to have the same set up through IPS 
* (Rajiv) VPN, 2 factor-authentication (NU) for underlay

> Underlay : admin access ; switches etc., management of storage, servers, networking

> Overlay: users rest API on controller nodes, access their VMs. Use Amazon model ?
