1. Connect to the vpn
2. Launch libvirt, either on the machine with x-forwarding or on personal and add eos-ctl01.rc.fas.harvard.edu as an additional connection
3. Launch new vm, choose iso from iso directory
4. If you want to touch the openstack cluster, make sure you are on bridge br0. If you want the outside world, then you want br.2444, ceph and other storage should be on br.600.
5. Install and launch vm. If you are on internal network, make sure dhcp is set to yes on boot in /etc/sysconfig/network-scripts/[interface]