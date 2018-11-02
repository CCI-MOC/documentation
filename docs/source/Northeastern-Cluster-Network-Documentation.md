(up) [[Northeastern Cluster]]

* [VLANs and IP Addresses](#vlans-and-ip-addresses)
* [Haas Master Config](#haas-master-config)
* [Special Haas Networks](#special-haas-networks)
* [Diagrams](#diagrams)

## VLANs and IP Addresses

On 1G Junniper switches
* [VLAN 100](#vlan-100) OBM/Management 10.99.1.0/24
* [VLAN 101](#vlan-101)  Externally Visible Management 129.10.3.248/29

On 10G Cisco switches
* [VLAN 127](#vlan-127)  Externally Visible Production(#vlan-127) 129.10.5.0/24
* [VLAN 250](#vlan-250)  Fujitsu Storage (Ceph client network) 192.168.28.0/24
* [VLAN 999](#vlan-999)  Openstack private 10.13.0.0/22
* [VLAN 1004](#vlan-1004)  Foreman Provisioning 10.13.37.0/24
* [VLAN 3802](#vlan-3802) CSAIL floating IPs

###### 10.99.1.0/24:  VLAN 100  [OBM/MANAGEMENT VLAN]<a name="vlan-100"></a>

     10.99.1.1-4:   Kzn-osd01-04, Fujitsu servers, admin,admin, KVM works with Java 6, IE11
     10.99.1.5:     Cisco Nexus 5672 in Cabinet 17
     10.99.1.6:     Cisco Nexus 5672 in Cabinet 19
     10.99.1.14:    Fujitsu MGT Port #1 (User/Password: admin/3YKHDPQgmMkf1)
     10.99.1.15:    Fujitsu MGT Port #2
     10.99.1.16:    Emergency box (Micro Server) IP address
     10.99.1.21-27:  Dell ceph nodes
     10.99.1.101-148:  Cisco server OBMs
                       (User/Password: admin/73qtx8nVXa4c06)

     10.99.1.253    VM on HaaS master (henn)
     10.99.1.254    kzn-h.infra.massopen.cloud (Kaizen HIL)

###### 129.10.3.248/29: VLAN 101  [EXTERNALLY VISIBLE MANAGEMENT VLAN]<a name="vlan-101"></a>

     129.10.3.249    Gateway
     129.10.3.250    Reserved for switch stack loopback
     129.10.3.251    Supermicro "emergency rescue" box   emergency.kaizen.massopencloud.org
     129.10.3.252       ---available---
     129.10.3.253    Fujitsu remote management ("support")
     129.10.3.254       ---available---

###### 129.10.5.0/25: VLAN 127  [EXTERNALLY VISIBLE PRODUCTION VLAN]

     129.10.5.1         Gateway for 129.10.5.0/25
     129.10.5.2        Switch 1 Gateway (129.10.5.0/25)
     129.10.5.3        Switch 2 Gateway (129.10.5.0/25)
     129.10.5.4        kzn-hil-client.infra.massopen.cloud
     129.10.5.5        kzn-ssh
     129.10.5.8        free?
     129.10.5.{9-15}   OpenStack Floating IPs
     129.10.5.16       Node 16 - staging area services
     120.10.5.{17-31}  OpenStack Floating IPs
     129.10.5.32       Reservered for 2nd Controller Node 32
     129.10.5.{33-35}
     120.10.5.36       Reserved?? (Laura investigating)
     129.10.5.{37-38}  OpenStack Floating IPs
     129.10.5.39       Node 39 - Kaizen production services node
     129.10.5.{40-43}  OpenStack Floating IPs
     129.10.5.{44-46}  -- unused ---
     129.10.5.47       OpenStack Controller
     129.10.5.49       IPFIRE gateway attached to huy-network serving Huy from BU BUILDs (see Evan or Jason)
     129.10.5.50       staging area 2nd controller
     129.10.5.51       production horizon
     129.10.5.52       Redhat repo
     129.10.5.53       (temporary testing for radosgw)
     129.10.5.54       production radosgw (rdgw.kaizen.massopencloud.org)
     129.10.5.{57,58}  --- unused --
     129.10.5.{65-254} OpenStack Floating IPs


###### 192.168.28.0/24: VLAN 250 [FUJITSU STORAGE VLAN]<a name="vlan-250"></a>
*Note: Addresses on this network are all pre-assigned in /etc/hosts on the Fujitsu nodes, even though in most cases the relevant machine doesn't actually exist.  So when debugging this network, you occasionally see a weird hostname resolve on the Fujitsu end.*

     192.168.28.{11-14} Fujitsu Storage Nodes
     192.168.28.{1-10}  reserved for compute nodes until setup script is fixed*
     192.168.28.{15-19} reserved for compute nodes until setup script is fixed*
     192.168.28.20      ceph-iscsi-gateway (VM on Haas Master - sometimes 192.168.28.97)
     192.168.{21-48}    reserved for compute nodes until setup script is fixed*
     192.168.28.53      kaizen-radosgw-hammer01
     192.168.28.54      kaizen-radosgw-hammer02 (non-production, used by Vinay and Jeremy)
     192.168.28.55      bmi-radosgw
     192.168.28.59      ATLAS headnode
     192.168.28.77      node 47 (controller)
     192.168.28.96      reserved for old production radosgw, awaiting decommissioning (see Laura K)
     192.168.28.97      ceph-iscsi-gateway (VM on Haas Master - formerly 192.168.28.20, see Jason)
     192.168.28.98      iscsi-hpc-gateway (VM on Haas Master, see Evan)
     192.168.28.130     BMI-Infra-VM (VM on Haas Master, see Ravi.G)

###### 10.13.37.0/24: VLAN 1004 [FOREMAN PROVISIONING VLAN] <a name="vlan-1004"></a>*

	10.13.37.1     foreman
	10.13.37.2     contr1test-71
	10.13.37.3     compute-69
	10.13.37.4     node-47
	10.13.37.5     compute-46
	10.13.37.6     compute-38
	10.13.37.7     compute-28
	10.13.37.8     compute-24
	10.13.37.9     compute-42
	10.13.37.10    comp1test-70
	10.13.37.11    compute-29
	10.13.37.13    compute-30
	10.13.37.14    contr2test
	10.13.37.15    compute-31
	10.13.37.17    compute-32
	10.13.37.21    compute-36
	10.13.37.23    compute-43
	10.13.37.25    compute-25
	10.13.37.28    compute-40
	10.13.37.29    compute-41
	10.13.37.31    compute-39
	10.13.37.33    compute-37
	10.13.37.35    compute-35
	10.13.37.37    compute-34
	10.13.37.39    compute-17
	10.13.37.41    compute-02
	10.13.37.43    compute-01
	10.13.37.55    compute-07
	10.13.37.56    compute-09
	10.13.37.57    compute-08
	10.13.37.58    compute-14
	10.13.37.59    compute-10
	10.13.37.60    compute-11
	10.13.37.61    compute-13
	10.13.37.62    compute-12
	10.13.37.63    compute-15
	10.13.37.65    compute-18
	10.13.37.67    compute-16 (now it's compute-48 aka kzn-h)
	10.13.37.69    compute-04
	10.13.37.72    compute-19
	10.13.37.73    compute-20
	10.13.37.75    compute-21
	10.13.37.77    compute-22
	10.13.37.79    compute-23
	10.13.37.96    compute-06
	10.13.37.107   compute-33
	10.13.37.134   compute-182
	10.13.37.136   compute-183


\* see [this issue](https://github.com/CCI-MOC/kilo-puppet/issues/11)


## Diagrams

![](NUManagementNetworkTopology.png)
![](NUclusterNetworkTopology.png)


### 1G Switches
129.10.3.249, admin, Wittinglypreconfigure

IPMI vlan switchports
```
    VLAN-MOC-100 {
        description "MOC VLAN";
        vlan-id 100;
        interface {
            ge-0/0/0.0;
            ge-0/0/1.0;
            ge-0/0/2.0;
            ge-0/0/3.0;
            ge-0/0/4.0;
            ge-0/0/5.0;
            ge-0/0/6.0;
            ge-0/0/7.0;
            ge-0/0/8.0;
            ge-0/0/9.0;
            ge-0/0/10.0;
            ge-0/0/11.0;
            ge-0/0/12.0;
            ge-0/0/13.0;
            ge-0/0/14.0;
            ge-0/0/15.0;
            ge-0/0/16.0;
            ge-0/0/17.0;
            ge-0/0/18.0;
            ge-0/0/19.0;
            ge-0/0/20.0;
            ge-0/0/21.0;
            ge-0/0/22.0;
            ge-0/0/23.0;
            ge-0/0/24.0;
            ge-0/0/25.0;
            ge-0/0/26.0;
            ge-0/0/27.0;
            ge-0/0/28.0;
            ge-0/0/29.0;
            ge-0/0/32.0;
            ge-0/0/33.0;
            ge-0/0/34.0;
            ge-0/0/35.0;
            ge-0/0/36.0;
            ge-0/0/37.0;
            ge-0/0/38.0;
            ge-0/0/39.0;
            ge-0/0/40.0;
            ge-0/0/41.0;
            ge-0/0/42.0;
            ge-0/0/43.0;
            ge-0/0/44.0;
            ge-0/0/45.0;
            ge-0/0/47.0;
            ge-1/0/0.0;
            ge-1/0/1.0;
            ge-1/0/2.0;
            ge-1/0/3.0;
            ge-1/0/4.0;
            ge-1/0/5.0;
            ge-1/0/6.0;
            ge-1/0/7.0;
            ge-1/0/8.0;
            ge-1/0/9.0;
            ge-1/0/10.0;
            ge-1/0/11.0;
            ge-1/0/12.0;
            ge-1/0/13.0;
            ge-1/0/14.0;
            ge-1/0/15.0;
            ge-1/0/16.0;
            ge-1/0/17.0;
            ge-1/0/18.0;
            ge-1/0/19.0;
            ge-1/0/20.0;
            ge-1/0/21.0;
            ge-1/0/22.0;
            ge-1/0/23.0;
            ge-1/0/24.0;
            ge-1/0/25.0;
            ge-1/0/26.0;
            ge-1/0/27.0;
            ge-1/0/28.0;
            ge-1/0/29.0;
            ge-1/0/32.0;
            ge-1/0/33.0;
            ge-1/0/34.0;
            ge-1/0/35.0;
            ge-1/0/36.0;
            ge-1/0/37.0;
            ge-1/0/38.0;
            ge-1/0/39.0;
            ge-1/0/40.0;
            ge-1/0/41.0;
            ge-1/0/42.0;
            ge-1/0/43.0;
            ge-1/0/44.0;
            ge-1/0/45.0;
            ge-1/0/46.0;
            inactive: ge-1/0/47.0;
            ge-1/0/30.0;
            ge-0/0/31.0;
            ge-1/0/31.0;
        }

```
