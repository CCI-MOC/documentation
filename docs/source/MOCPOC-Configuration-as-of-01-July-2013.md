We now have two switches: an unmanaged "public" switch, and a fancy managed "management" switch.  The unmanaged "public" switch can reach the internet through moc-gateway on death-star.  The onboard ethernet of each Optiplex is on the management network, and their 5 dollar PCI ethernet cards are on the public network.

Our cluster is partitioned into four sub-clusters, each comprising three nodes.  Each is on a separate VLAN on the management network.  We have a four-way KVM switch, attached to the lowest-numbered node in each subcluster.  The subclusters are as follows:

    [{nodes=[1, 3, 5],   vlan=101,  kvm=1}, 
     {nodes=[2, 4, 6],   vlan=102,  kvm=2},
     {nodes=[7, 9, 11],  vlan=107,  kvm=3},
     {nodes=[8, 10, 12], vlan=108,  kvm=4}]

death-star is on all four of these VLANs.  It is not set up to PXE boot anything on them, leaving you free to set up your own PXE server on a subcluster (e.g. with Fuel).  Note that PXE booting happens only on the management network.

death-star can reach the management interface of the managed switch at 192.168.0.1 .  This address only works on ports 13-16 of the managed switch.

death-star can reach the Intel management extensions of the Optiplexes (Optiplices?) at 10.0.0.1 through 10.0.0.12 .  It has a specific route in its routing table for each of these IP addresses.

Again, the public network is NATted behind the moc-gateway VM on death-star.  If you DHCP on that network, you will get an address in 192.168.3.0/24, corresponding directly to the machine number.