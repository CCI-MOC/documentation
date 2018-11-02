#### Listing all the flows present in vswitch
This is the default flow present which allows all the traffic.
```
[root@compute-202-tammy libvirt]# ovs-ofctl dump-flows br-cache
NXST_FLOW reply (xid=0x4):
 cookie=0x0, duration=2005.504s, table=0, n_packets=1435, n_bytes=133878, idle_age=216, priority=0 actions=NORMAL
[root@compute-202-tammy libvirt]# 
```

#### When a vlan tag is added to a particular port
Let's add a vlan tag to the nic of vm attached to openvswitch. This can be achieved by adding this to the libvirt XML:-
```
  <interface id=...>
      ...
      <vlan>
        <tag id='2001'/>
      </vlan>
      ...
  </interface>
```
This puts the vm on vlan 2001.
```
# ovs-vsctl show

    Bridge br-cache
        Port br-cache
            Interface br-cache
                type: internal
        Port cache-sxyurrqj
            tag: 2001               <----- tagged with vlan 2001
            Interface cache-sxyurrqj
        Port cache-taoogliq
            tag: 2002               <----- tagged with vlan 2002
            Interface cache-taoogliq
        Port cache-etqzozoc
            tag: 2002               <----- tagged with vlan 2002
            Interface cache-etqzozoc
        Port cache-uoildwal
            tag: 2001               <----- tagged with vlan 2001
            Interface cache-uoildwal
```
To tag the port as trunk and add multiple vlans, one can use:
```
      <vlan trunk='yes'>
        <tag id='2001'/>
        <tag id='2002'/>
        <tag id='2003'/>
      </vlan>
```