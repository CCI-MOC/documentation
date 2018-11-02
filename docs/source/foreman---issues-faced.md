129 net connectivity - 

hardware iface ifcfg script (`/etc/sysconfig/network-scripts/ifcfg-enp130s0f0`):
```
BOOTPROTO="dhcp"
DEVICE="enp130s0f0"
HWADDR="90:e2:ba:8e:bd:64"
ONBOOT=yes
PEERDNS=no
DEFROUTE=no
PEERROUTES=no
```

virtual tagged iface ifcfg script (`/etc/sysconfig/network-scripts/ifcfg-enp130s0f0.125`):
```
VLAN=yes
TYPE=Vlan
BOOTPROTO=static
DEVICE="enp130s0f0.125"
NAME="enp130s0f0.125"
ONBOOT=yes
MTU=9000
IPADDR=129.10.3.47
NETMASK=255.255.255.0
GATEWAY=129.10.3.1
DNS1=8.8.8.8
```

Manually added OSP repo, and subscribed box to RHN.
osp.repo:
```
[osp]
name=osp
baseurl=ftp://partners.redhat.com/9be6fa88fb85ba9c78c8f3fe47e038c5/OpenStack/7.0-RHEL-7/2015-06-24.1/RH7-RHOS-7.0/x86_64/os/
gpgcheck=0
enabled=1
```

steps to subscribe:
```
# subscription-manager register
# subscription-manager attach --auto
# subscription-manager list
```