## Debugging neutron for Kilo RHOSP-7.1 RHEL 7.1 set-up

### Useful Links
* http://docs.openstack.org/admin-guide-cloud/content/ch_networking.html
* http://docs.openstack.org/openstack-ops/content/network_troubleshooting.html
* https://www.rdoproject.org/Networking_in_too_much_detail
* http://www.yet.org/2014/09/openvswitch-troubleshooting/

### Useful Commands
#### 

#### ovs-vsctl - show and modify virtual switch interfaces
* show commands
  * ovs-vsctl show - shows full config
  * ovs-vsctl br-show [bridge]
  * ovs-vsctl port-show [port]

#### ovs-ofctl
  * ovs-ofctl dump-flows [bridge]

### Status on production cluster
[7/17/15] DHCP still not working. Assigning a static IP address works, and allows pinging the router. Investigating why BigData cluster can DHCP and production can't, by comparing the two.

### Status on BigData cluster
[7/20/15] UPDATE: brought enp130s0f0.125 & enp130s0f0 down, brought back up enp130s0f0, enp130s0f0.125 was brought up with it, if fixed the issue. Apparently they have to be brought together based on http://www.linuxquestions.org/questions/linux-networking-3/rtnetlink-answers-file-exists-error-when-doing-ifup-on-alias-eth1-1-on-rhel5-710766/
[7/20/15] Tried installing tcpdump to test interfaces. 30 and 31 can no longer ping internet.
[7/17/15] DHCP'ing and connecting to router successfully. SSH and large pings are not getting throught. Thought to be an MTU issue.

### Comparison of compute 29 (production) and compute 31 (big data)
#### ovs-vsctl show - list of ovs bridges
```
[root@compute-29 ~]# ovs-vsctl show
e309a501-ce92-4866-bcb8-ade421786753
    Bridge br-tun
        fail_mode: secure
        Port patch-int
            Interface patch-int
                type: patch
                options: {peer=patch-tun}
        Port "snooper0"
            Interface "snooper0"
        Port "vxlan-0a0d2504"
            Interface "vxlan-0a0d2504"
                type: vxlan
                options: {df_default="true", in_key=flow, local_ip="10.13.37.11", out_key=flow, remote_ip="10.13.37.4"}
        Port br-tun
            Interface br-tun
                type: internal
    Bridge br-int
        fail_mode: secure
        Port int-br-ex
            Interface int-br-ex
                type: patch
                options: {peer=phy-br-ex}
        Port "qvo2ab8889a-e7"
            tag: 1
            Interface "qvo2ab8889a-e7"
        Port "qvo978b468e-5f"
            tag: 1
            Interface "qvo978b468e-5f"
        Port "qvo501b86a7-5f"
            tag: 1
            Interface "qvo501b86a7-5f"
        Port br-int
            Interface br-int
                type: internal
        Port patch-tun
            Interface patch-tun
                type: patch
                options: {peer=patch-int}
        Port "qvo98a118d7-96"
            tag: 1
            Interface "qvo98a118d7-96"
    Bridge br-ex
        Port br-ex
            Interface br-ex
                type: internal
        Port phy-br-ex
            Interface phy-br-ex
                type: patch
                options: {peer=int-br-ex}
        Port "enp130s0f0.125"
            Interface "enp130s0f0.125"
    ovs_version: "2.3.1-git3282e51"
```
```
[root@compute-31 ~]# ovs-vsctl show
d1a2649b-9908-4b40-86ff-77239f4c5f64
    Bridge br-int
        fail_mode: secure
        Port patch-tun
            Interface patch-tun
                type: patch
                options: {peer=patch-int}
        Port "qvo2d968a06-69"
            tag: 3
            Interface "qvo2d968a06-69"
        Port "qvo2de74459-85"
            tag: 3
            Interface "qvo2de74459-85"
        Port "qvo867c2b00-a5"
            tag: 3
            Interface "qvo867c2b00-a5"
        Port br-int
            Interface br-int
                type: internal
        Port int-br-ex
            Interface int-br-ex
                type: patch
                options: {peer=phy-br-ex}
        Port "qvoc414a653-53"
            tag: 3
            Interface "qvoc414a653-53"
    Bridge br-ex
        Port phy-br-ex
            Interface phy-br-ex
                type: patch
                options: {peer=int-br-ex}
        Port "enp130s0f0.125"
            Interface "enp130s0f0.125"
        Port br-ex
            Interface br-ex
                type: internal
    Bridge br-tun
        fail_mode: secure
        Port br-tun
            Interface br-tun
                type: internal
        Port "vxlan-0a0d250d"
            Interface "vxlan-0a0d250d"
                type: vxlan
                options: {df_default="true", in_key=flow, local_ip="10.13.37.15", out_key=flow, remote_ip="10.13.37.13"}
        Port patch-int
            Interface patch-int
                type: patch
                options: {peer=patch-tun}
    ovs_version: "2.3.1-git3282e51"
```
#### ovs-ofctl show br-tun - more info on br-tun
```
[root@compute-29 ~]# ovs-ofctl show br-tun
OFPT_FEATURES_REPLY (xid=0x2): dpid:00002a89dc75b140
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: OUTPUT SET_VLAN_VID SET_VLAN_PCP STRIP_VLAN SET_DL_SRC SET_DL_DST SET_NW_SRC SET_NW_DST SET_NW_TOS SET_TP_SRC SET_TP_DST ENQUEUE
 4(patch-int): addr:9e:39:38:9b:b9:04
     config:     0
     state:      0
     speed: 0 Mbps now, 0 Mbps max
 5(vxlan-0a0d2504): addr:32:7d:b2:97:19:56
     config:     0
     state:      0
     speed: 0 Mbps now, 0 Mbps max
 6(snooper0): addr:fa:81:a7:98:e2:ed
     config:     0
     state:      0
     speed: 0 Mbps now, 0 Mbps max
 LOCAL(br-tun): addr:2a:89:dc:75:b1:40
     config:     0
     state:      0
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
```
```
[root@compute-31 ~]# ovs-ofctl show br-tun
OFPT_FEATURES_REPLY (xid=0x2): dpid:000022939cf9f048
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: OUTPUT SET_VLAN_VID SET_VLAN_PCP STRIP_VLAN SET_DL_SRC SET_DL_DST SET_NW_SRC SET_NW_DST SET_NW_TOS SET_TP_SRC SET_TP_DST ENQUEUE
 1(patch-int): addr:76:b4:25:ca:20:6e
     config:     0
     state:      0
     speed: 0 Mbps now, 0 Mbps max
 2(vxlan-0a0d250d): addr:7a:65:c3:ec:8e:8c
     config:     0
     state:      0
     speed: 0 Mbps now, 0 Mbps max
 LOCAL(br-tun): addr:22:93:9c:f9:f0:48
     config:     0
     state:      0
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
```
#### ovs-ofctl dump-flows br-tun - show flow tables for bridges
```
[root@compute-29 ~]# ovs-ofctl dump-flows br-tun
NXST_FLOW reply (xid=0x4):
 cookie=0x0, duration=91613.635s, table=0, n_packets=0, n_bytes=0, idle_age=65534, hard_age=65534, priority=0 actions=drop
 cookie=0x0, duration=91612.822s, table=0, n_packets=227, n_bytes=22246, idle_age=65534, hard_age=65534, priority=1,in_port=5 actions=resubmit(,4)
 cookie=0x0, duration=91613.647s, table=0, n_packets=9476, n_bytes=3049344, idle_age=19, hard_age=65534, priority=1,in_port=4 actions=resubmit(,2)
 cookie=0x0, duration=91613.631s, table=2, n_packets=0, n_bytes=0, idle_age=65534, hard_age=65534, priority=0,dl_dst=00:00:00:00:00:00/01:00:00:00:00:00 actions=resubmit(,20)
 cookie=0x0, duration=91613.627s, table=2, n_packets=9476, n_bytes=3049344, idle_age=19, hard_age=65534, priority=0,dl_dst=01:00:00:00:00:00/01:00:00:00:00:00 actions=resubmit(,22)
 cookie=0x0, duration=91613.624s, table=3, n_packets=0, n_bytes=0, idle_age=65534, hard_age=65534, priority=0 actions=drop
 cookie=0x0, duration=91613.620s, table=4, n_packets=0, n_bytes=0, idle_age=65534, hard_age=65534, priority=0 actions=drop
 cookie=0x0, duration=91612.439s, table=4, n_packets=227, n_bytes=22246, idle_age=65534, hard_age=65534, priority=1,tun_id=0xa actions=mod_vlan_vid:1,resubmit(,10)
 cookie=0x0, duration=91613.616s, table=10, n_packets=227, n_bytes=22246, idle_age=65534, hard_age=65534, priority=1 actions=learn(table=20,hard_timeout=300,priority=1,NXM_OF_VLAN_TCI[0..11],NXM_OF_ETH_DST[]=NXM_OF_ETH_SRC[],load:0->NXM_OF_VLAN_TCI[],load:NXM_NX_TUN_ID[]->NXM_NX_TUN_ID[],output:NXM_OF_IN_PORT[]),output:4
 cookie=0x0, duration=91613.613s, table=20, n_packets=0, n_bytes=0, idle_age=65534, hard_age=65534, priority=0 actions=resubmit(,22)
 cookie=0x0, duration=91613.609s, table=22, n_packets=8, n_bytes=648, idle_age=65534, hard_age=65534, priority=0 actions=drop
 cookie=0x0, duration=91612.443s, table=22, n_packets=9468, n_bytes=3048696, idle_age=19, hard_age=65534, dl_vlan=1 actions=strip_vlan,set_tunnel:0xa,output:5
```
```
[root@compute-31 ~]# ovs-ofctl dump-flows br-tun
NXST_FLOW reply (xid=0x4):
 cookie=0x0, duration=266021.260s, table=0, n_packets=1, n_bytes=70, idle_age=65534, hard_age=65534, priority=0 actions=drop
 cookie=0x0, duration=266021.273s, table=0, n_packets=161068, n_bytes=21623318, idle_age=3, hard_age=65534, priority=1,in_port=1 actions=resubmit(,2)
 cookie=0x0, duration=96931.696s, table=0, n_packets=129238, n_bytes=16689287, idle_age=3, hard_age=65534, priority=1,in_port=2 actions=resubmit(,4)
 cookie=0x0, duration=266021.256s, table=2, n_packets=160003, n_bytes=21488443, idle_age=3, hard_age=65534, priority=0,dl_dst=00:00:00:00:00:00/01:00:00:00:00:00 actions=resubmit(,20)
 cookie=0x0, duration=266021.253s, table=2, n_packets=1065, n_bytes=134875, idle_age=1657, hard_age=65534, priority=0,dl_dst=01:00:00:00:00:00/01:00:00:00:00:00 actions=resubmit(,22)
 cookie=0x0, duration=266021.250s, table=3, n_packets=0, n_bytes=0, idle_age=65534, hard_age=65534, priority=0 actions=drop
 cookie=0x0, duration=101991.350s, table=4, n_packets=129408, n_bytes=16706942, idle_age=3, hard_age=65534, priority=1,tun_id=0xc actions=mod_vlan_vid:3,resubmit(,10)
 cookie=0x0, duration=266021.247s, table=4, n_packets=43, n_bytes=3486, idle_age=6067, hard_age=65534, priority=0 actions=drop
 cookie=0x0, duration=266021.243s, table=10, n_packets=142163, n_bytes=18113007, idle_age=3, hard_age=65534, priority=1 actions=learn(table=20,hard_timeout=300,priority=1,NXM_OF_VLAN_TCI[0..11],NXM_OF_ETH_DST[]=NXM_OF_ETH_SRC[],load:0->NXM_OF_VLAN_TCI[],load:NXM_NX_TUN_ID[]->NXM_NX_TUN_ID[],output:NXM_OF_IN_PORT[]),output:1
 cookie=0x0, duration=266021.239s, table=20, n_packets=234, n_bytes=21069, idle_age=162, hard_age=65534, priority=0 actions=resubmit(,22)
 cookie=0x0, duration=9074.652s, table=20, n_packets=3503, n_bytes=346604, hard_timeout=300, idle_age=3, hard_age=3, priority=1,vlan_tci=0x0003/0x0fff,dl_dst=fa:16:3e:80:d4:4b actions=load:0->NXM_OF_VLAN_TCI[],load:0xc->NXM_NX_TUN_ID[],output:2
 cookie=0x0, duration=162.923s, table=20, n_packets=1, n_bytes=42, hard_timeout=300, idle_age=157, hard_age=157, priority=1,vlan_tci=0x0003/0x0fff,dl_dst=fa:16:3e:b9:10:c7 actions=load:0->NXM_OF_VLAN_TCI[],load:0xc->NXM_NX_TUN_ID[],output:2
 cookie=0x0, duration=266021.229s, table=22, n_packets=65, n_bytes=5354, idle_age=1672, hard_age=65534, priority=0 actions=drop
 cookie=0x0, duration=101991.354s, table=22, n_packets=658, n_bytes=88005, idle_age=162, hard_age=65534, dl_vlan=3 actions=strip_vlan,set_tunnel:0xc,output:2
```