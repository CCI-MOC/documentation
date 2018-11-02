## Reference CLI commands for Midonet

#### List Routers and chains
```
[root@controller-midonet-209 ~(keystone_admin)]# midonet-cli
midonet> router list
midonet> chain list
```
#### List information of all ports within a router
```
midonet> router router0 list port
port port0 device router0 state up plugged no mac fa:16:3e:e9:49:18 address 192.168.1.1 net 192.168.1.0/24 peer bridge0:port0
port port1 device router0 state up plugged no mac fa:16:3e:28:a4:a1 address 203.0.113.101 net 203.0.113.0/24 peer bridge1:port0
midonet>
```
#### Adding ip-address to a port on the router
```
midonet> router router0 add port address 172.19.0.2 net 172.19.0.0/30
router0:port0
```
This adds an ip-address of 172.19.0.2 to router router0
#### Adding route on the router
```
midonet> router router0 add route src 0.0.0.0/0 dst 0.0.0.0/0 type normal port router router0 port port0 gw 172.19.0.1
```
#### Adding one end of veth pair cable to router
```
# ip link add type veth
# ip link set dev veth0 up
# ip link set dev veth1 up
midonet> host host0 add binding port router router0 port port0 interface veth1
```
#### Creating a tunnel zone
```
midonet> create tunnel-zone name tz1 type gre
tzone0
midonet> list tunnel-zone
tzone tzone0 name tz1 type gre
midonet> delete tunnel-zone tz1
midonet> tunnel-zone tzone0 list member
zone tzone0 host host0 address 10.14.37.210
midonet> 
```
#### Hosts and commands related to hosts
```
midonet> list host
host host0 name compute-midonet-210.staging.moc.edu alive true addresses fe80:0:0:0:5054:ff:fec1:39d2,fe80:0:0:0:5054:ff:fec1:39d2,10.14.37.210,127.0.0.1,0:0:0:0:0:0:0:1,fe80:0:0:0:48dd:feff:fe18:43ef,169.254.169.254,fe80:0:0:0:41d:bdff:fe4f:81b5,172.24.4.225,fe80:0:0:0:40fc:9bff:fe3c:d242 flooding-proxy-weight 1


midonet> host host0 list interface
iface eth2.124 host_id host0 status 3 addresses [u'fe80:0:0:0:5054:ff:fec1:39d2'] mac 52:54:00:c1:39:d2 mtu 1500 type Virtual endpoint UNKNOWN
iface eth2 host_id host0 status 3 addresses [u'fe80:0:0:0:5054:ff:fec1:39d2', u'10.14.37.210'] mac 52:54:00:c1:39:d2 mtu 1500 type Virtual endpoint DATAPATH
iface midonet host_id host0 status 2 addresses [] mac 42:7d:a1:6f:a7:69 mtu 1500 type Virtual endpoint UNKNOWN
iface lo host_id host0 status 3 addresses [u'127.0.0.1', u'0:0:0:0:0:0:0:1'] mac 00:00:00:00:00:00 mtu 65536 type Virtual endpoint LOCALHOST
iface br-int host_id host0 status 2 addresses [] mac d2:93:89:08:7e:45 mtu 1500 type Virtual endpoint UNKNOWN
iface tapa73437a4-ca host_id host0 status 3 addresses [u'fe80:0:0:0:48dd:feff:fe18:43ef'] mac 4a:dd:fe:18:43:ef mtu 65000 type Virtual endpoint TUNTAP
iface metadata host_id host0 status 3 addresses [u'169.254.169.254', u'fe80:0:0:0:41d:bdff:fe4f:81b5'] mac 06:1d:bd:4f:81:b5 mtu 1500 type Virtual endpoint UNKNOWN
iface br-tun host_id host0 status 2 addresses [] mac f2:8e:88:59:ff:47 mtu 1500 type Virtual endpoint UNKNOWN
iface ovs-system host_id host0 status 2 addresses [] mac b6:24:9d:78:54:e2 mtu 1500 type Virtual endpoint UNKNOWN
iface br-ex host_id host0 status 3 addresses [u'172.24.4.225', u'fe80:0:0:0:40fc:9bff:fe3c:d242'] mac 42:fc:9b:3c:d2:42 mtu 1500 type Virtual endpoint UNKNOWN


midonet> host host0 list binding
host host0 interface tapa73437a4-ca port bridge0:port1
midonet> 
```
###### What is router0 router1 when we do "router list"?
the real router IDs are not router0 router1 but uuids of the form `07c6ecbc-d81c-4680-a682-a126b085f4d7`
so, if you run `midonet-cli -e router list`, you'd see the uuids for each router
when you run midonet-cli in interactive mode
and do `router list` the client is assigning a router0, router1, etc to each router
and then you can refer to the routers by these router0, router1 names, instead of using uuids
```
[root@compute-midonet-210 ~]# midonet-cli -e router list
router eaec4594-7f20-4cb0-8a6b-0818b726d3b2 name demo-router state up infilter 22a1f463-4a74-489b-8410-30ea70253dd8 outfilter e8e5056f-538c-4cae-9591-f1d8ea226696 asn -1 forward-chain 33e7597a-c23b-49ae-bf8e-d2adf04c0838
```
###### Router related commands
```
midonet> router list
midonet> router router0 route list
midonet> router router0 port list
```