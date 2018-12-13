This page outlines the networking configuration of the MOC POC.

# Overview

The network consists of the following:

 - A VM host called death-star, with 32 GiB of ram, 4 CPU cores, and 6 NICs.
   - Hosts several VMs with important roles in the network, all named moc-*.
 - One 16-port unmanaged switch
 - One 16-port managed switch
 - 12 inherited Dell Optiplex 760 workstations, with 4 GiB of RAM and 2 NICs each

The unmanaged switch provides a "public," (but behind a NAT) network. We treat this as if it were on the public internet; The NAT's purpose is to ensure that we don't cause problems for other users of our BU subnet if we e.g. misconfigure a dhcp server. Machines on this network are in the 192.168.3.0/24 range, and we refer to this as "nat-public".

The managed switch is used to isolate "tenants" in the network - each of which consists of a [[head node|Vlan Head Nodes]], and a subset of the dell workstations. It uses vlans for this purpose. port 16 on the managed switch is hooked in to death-star, and configured as trunking. (the relevant nic on death-star is vlan aware, and death-star is responsible for routing vlan traffic to the correct head node.)

A few machines are connected directly to BU, and have globally routable ip addresses. These are "public" ip addresses.

# Individual machine configurations.

## Owned static ips
* Name: moc-compute.bu.edu, Address: 128.197.44.203
* Name: moc-control.bu.edu, Address: 128.197.44.202

## MOCPOC cluster IPs:

 - death-star ([[Death Star VM configuration]])
   - nat-public : 192.168.3.254
   - public: 128.197.44.203

 - VMs on death-star:
   - moc-dhcp
     - nat-public 192.168.3.253
     - serves reserved ip addresses to each of the hardware nodes, along with a few virtual machines
     - serves the range 192.168.3.64-127 to other machines.

   - moc-dns
     - nat-public 192.168.3.248

   - moc-nfs
     - nat-public 192.168.3.252

   - moc-ntp
     - nat-public 192.168.3.249

   - moc-gateway
     - nat-public 192.168.3.250

 - All compute nodes: 
   - eth0 is connected to the private network and the IP assigned from the moc-dhcp DHCP server.
   - moc-dhcp gives private address in range 192.168.3.1-31

