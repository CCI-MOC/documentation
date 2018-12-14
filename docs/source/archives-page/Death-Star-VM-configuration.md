We're running four VMs on death-star, using virt-manager:  moc-dhcp, moc-nfs, moc-nova and moc-gateway.  All are set to autostart.  All machines used bridged networking, on one or both of the two bridged networks on death-star.

*  br0 bridges to eth0 (the internal network).  death-star has two static IPs here: 10.0.0.254 and 192.168.3.254
*  br1 bridges to eth1 (BU's network).  death-star has a static IP here as well: 128.197.44.203

To connect a machine to one or both networks, add a NIC to them, and "specify shared device name" as either br0 or br1.

These three machines are all clones of ubuntu-base, and I recommend that any future required machines also be clones.  (Please never actually boot ubuntu-base: we want it to be in a consistent state for cloning.)

There is one more VM, moc-test-machine.  It's set up to PXE boot, to check that our boot VMs are in a sane state.

__USING VIRT-MANAGER__

Can access or start machines on Deathstar through File>Add Connection
