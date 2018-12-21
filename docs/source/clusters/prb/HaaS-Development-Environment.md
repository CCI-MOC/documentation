# HaaS Development Environment
There are 3 haas environments in PRB. Nitty gritty network cabling and other info can be found in [PRB HaaS Cluster](BU-PRB-Cluster.html).
=======
This document describes new haas development setup in PRB. The HaaS master is essentially unchanged (and in fact needs to be updated to conform to our new system administration standards).

We're using the sun blades as worker nodes, and the dell switch, with the ports used as follows:
* 1,3,5,7: nic #0 on the first 4 nodes (counting from the top)
* 2,4,6,8: nic #1 on the first 4 nodes.
* 9: haas master eth1 (outside world); vlan 2
* 10: ipmi daisychain
* 11: haas master eth0 (trunk nic)
* 12: haas master eth3 (switch control)
* 13-20: nics on the remainder of the nodes
* 24: outside world; vlan 2

The Switch has ip address 192.168.3.245, which is visible on vlan 1.

Accordingly, port 12 (the haas master's switch control nic) is also on vlan 1.

Vlan 2 is exposed to the outside world. Right now this includes only the haas master's external nic.

### SSH gateway to MOC Intranet

  **Host:** moc-haas-master.bu.edu port 22222

The MOC Intranet is isolated from the Internet so that we can be a little more open about the haas services. To access the Intranet, one can use the ssh gateway by ssh'ing to: `moc-haas-master.bu.edu` port `22222`. If you are a haas dev, you can also access the DEV and DEPLOY environments directly from the Internet.

### HaaS envs

  **BETA** : haas-beta.prb.massopencloud.org (only accessible from within the MOC Intranet)

Location for users to try the latest/greatest haas bits on machines that aren't as high class as the production ones.

Machines placed on the moc-intranet network can reach the outside world by following the network config info found on [PRB HaaS Cluster](haas-dev-setup.html).

  **DEV** : [moc-haas-master.bu.edu](PRB-MOC-Haas-Master-Config.html) (accessible from Internet)

This is for active development/testing. HaaS is to be run using `haas serve`/`haas serve_networks`. If someone else is using, talk to them and ask them to kill their processes.

  **DEPLOY**: moc-haas-deploy.bu.edu (accessible from Internet)

This server is reserved for running the deployment tests.

