# BU PRB Cluster

### Nodes
  
  **Node names** in haas and other places  are {super,len}-\<last octet of IPMI IP\> and reflect the manufacturer.
  * Example: sun-10, super-42

  **Current ranges for nodes** : super-[36-46], dell-58 (moc01) dell(moc02),intel-[50-51], len-[60-63], think-[65-67].
  * We try to keep the numbers for particular node types together.
*If there is any confusion about who is using what, please talk to Naved.*

  **List of all nodes with config (scroll to the right to see everything)**

| ID | Node Name | Model Name | IPMI Address | RAM (GiB) | # of Sockets | # Processor Cores | Processor Model | Hyper-Threading | Who has it? | Notes |
| ---| --------- | ------------ | --------- | ------------ | ----------------- | --------------- | ----------- | ------- | -------- | -------- |
| 1 | cisco-200 | UCS C220 M3 | 10.10.0.200 | 192 | 2 | 6 | Xeon E-2630 |  Yes | Jeremy | "Gifting" to MOC from Cisco for Sahara CI
| 2 | cisco-201 | UCS C220 M3 | 10.86.1.201 |  96 | 2 | 6 | Xeon E-2630 | Yes | Cisco People | Owned by Cisco not MOC!
| 3 | cisco-202 | UCS C220 M3 | 10.86.1.202 |  96 | 2 | 6 | Xeon E-2630 | Yes | Cisco People | Owned by Cisco not MOC!
| 4 | cisco-203 | UCS C220 M3 | 10.86.1.203 |  96 | 2 | 6 | Xeon E-2630 | Yes | Cisco People | Owned by Cisco not MOC!
| 5 | cisco-204 | UCS C220 M3 | 10.10.0.204 | 160 | 2 | 6 | Xeon E-2630 | Yes | Jeremy | "Gifting" to MOC from Cisco for Sahara CI
| 6 | cisco-205 | UCS C220 M3 | 10.86.1.205 | 128 | 2 | 4 | Unknown | No | Cisco People | Owned by Cisco not MOC!
| 7 | moc01 (dell-58) | Dell PowerEdge R620 | 10.10.1.58 | 32 | 2 | 10 | Xeon E5-2670 v2 | Yes |  Naved | used as prb-services
| 8 | moc02 (dell-XX) | Dell PowerEdge R620 | Unknown | 32 | 2 | 10 | Xeon E5-2670 v2 | Yes | Amin | backing up Seccloud stuff from it
| 9 | hack-n-hil | Dell PowerEdge R610 | No dedicated IPMI | 32 | 2 | 4 | X5570  @ 2.93GHz | Yes | Amin | backing up Seccloud stuff from it
| 10 | super-36  | SYS-5018A-MLTN4 | 10.10.0.36 | 16 | 1 | 4 | Intel Atom C2550 2.4GHz | No | Naved | Haas Beta |
| 11 | super-37  | SYS-5018A-MLTN4 | 10.10.0.37 | 16 | 1 | 4 | Intel Atom C2550 2.4GHz | No | Free | - |
| 12 | super-38  | SYS-5018A-MLTN4 | 10.10.0.38 | 16 | 1 | 4 | Intel Atom C2550 2.4GHz | No | Ata Turk | PhD Students Yijia and Ozan are using this |
| 13 | super-39  | SYS-5018A-MLTN4 | 10.10.0.39 | 16 | 1 | 4 | Intel Atom C2550 2.4GHz | No | Kristi | Public IP: 128.197.43.194 |
| 14 | super-40  | SYS-5018A-MLTN4 | 10.10.0.40 | 16 | 1 | 4 | Intel Atom C2550 2.4GHz | No | Free | - |
| 15 | super-41  | SYS-5018A-MLTN4 | 10.10.0.41 | 16 | 1 | 4 | Intel Atom C2550 2.4GHz | No | Free | - |
| 16 | super-42  | SYS-5018A-MLTN4 | 10.10.0.42 | 16 | 1 | 4 | Intel Atom C2550 2.4GHz | No | Free | IPMI doesn't work |
| 17 | super-43  | SYS-5018A-MLTN4 | 10.10.0.43 | 16 | 1 | 4 | Intel Atom C2550 2.4GHz | No | Free | IPMI Down|
| 18 | super-44  | SYS-5018A-MLTN4 | 10.10.0.44 | 16 | 1 | 4 | Intel Atom C2550 2.4GHz | No | Naved | Used as moc-haas-deploy |
| 19 | super-45  | SYS-5018A-MLTN4 | 10.10.0.45 | 16 | 1 | 4 | Intel Atom C2550 2.4GHz | No | Free | IPMI Down|
| 20 | super-46  | SYS-5018A-MLTN4 | 10.10.0.46 | 16 | 1 | 4 | Intel Atom C2550 2.4GHz | No | Free | IPMI Down|
| 21 | intel-50  | S2600WTT | 10.10.0.50 | 128 | 2 | 14 | Xeon | Yes | Ian | For Foreman/M2 development |
| 22 | intel-51  | S2600WTT | 10.10.0.51 | 128 | 2 | 14 | Xeon | Yes | Naved | For his experiments |
| 23 | lenovo-60 | x3550 M5 | 10.10.1.60 | 128 | 2 | 10 | E5-2600 v4 | Yes |  SecCloud | IPMI Down
| 24 | lenovo-61 | x3550 M5 | 10.10.1.60 | 128 | 2 | 10 | E5-2600 v4 | Yes | SecCloud | IPMI Down
| 25 | lenovo-62 | x3550 M5 | 10.10.1.60 | 128 | 2 | 10 | E5-2600 v4 | Yes | SecCloud | - |
| 26 | HP Proliant | Proliant | Not configured | - | 1 | 4 | Xeon | - | Free | Really old machine with xeon processor.
| 27 | think-65 | Lenovo ThinkPad | Unknown | - | - | - | Unknown | - | SecCloud | it's a laptop
| 28 | think-65 | Lenovo ThinkPad | Unknown | - | - | - | Unknown | - | SecCloud | it's a laptop
| 29 | think-65 | Lenovo ThinkPad | Unknown | - | - | - | Unknown | - | SecCloud | it's a laptop

  **Notes on specific nodes**
  * think-[65-67] are used for Heads/Secure Cloud
  * think-65 is flashed to coreboot/Heads
  * think-66 is flashed and loaned to Gerardo Ravago
  * think-67 is still running stock firmware
  * Cisco-200 and Cisco-204 are registered with our central freeIPA server as prb-cisco-200.infra.massopen.cloud and prb-cisco-204.infra.massopen.cloud respectively.


  **KVM switch** :
There is a Keyboard Video Mouse (KVM) switch that can be used to administer nodes. You can switch which node's console you are using by pressing the "Print Screen" button, and selecting a port. 

The port number corresponds to which port the KVM dongle is connected to on the back of the KVM switch.

A data sheet describing its functionality can be found [here](https://lenovopress.com/tips0730).

All the cables to kvm have been disconnected to reduce clutter. It is recommended that you use IPMI to access the nodes, if the IPMI network is
down then please use the KVM switch.

### Network

  **Gateways** :
  * Gateway1: moc-haas-master.bu.edu
  * Gateway2: moc-haas-deploy.bu.edu

  To **pass information through the gateway**, you can use several methods:
  1. including "ssh -D" combined with your browser's SOCKS proxy support
  2. [sshuttle](https://sshuttle.readthedocs.io/en/stable/). A good command to transfer just traffic for the remote networks would be: `sshuttle -r user@ssh-gateway -N --dns`

  **Hardware** Our network is served by primarily 4 gigabit switches: 
  * **dell-{0,1,2,3}** (in the HIL rack with supermicros) and **tp-0** (located in the other rack)
  * dell-0 is the "hub" switch to which all other switches are connected. 
  * dell-0 has 10G connections to dell-1 and dell-2, and a regular 1G cable running to tp-0.

All of the switches have Multiple STP enabled to prevent loops.

  **Node Cluster Assignments**
  * **Ceph** : All supermicros will be used for deploying CEPH.
  * **VLANs 1-2500** are spanned to all switches, though on tp-0 individual VLANs may need to be made known to the switch before they can be used.
  * **VLANs 2501-4094** are not yet registered in order to preserve switch resources.

HIL instances are allocated 500 or less VLANs, starting at 1000. VLANs with an id <= 1000 are for other uses.

  **VLAN 1** : Network administrative interfaces (switch management).
  * **Subnet**: 192.168.3.0/24.
  * **Hosts** :
    * **Network-admin** (access to network switch administrative interfaces)
    * **192.168.3.2**  [[moc-haas-master.bu.edu|PRB-MOC-Haas-Master-Config]]
    * **192.168.3.93** moc-haas-deploy.bu.edu
    * **192.168.3.[170-200]** DHCP (served from moc-control)
    * **192.168.3.244** tp-0 (admin interface)
    * **192.168.3.245** Dell 0 (admin interface)
    * **192.168.3.246** Dell 1 (admin interface)
    * **192.168.3.247** Dell 2 (admin interface)
    * **192.168.3.250** Dell 3 (admin interface)

  **VLAN 2** : Public Internet (caution!)

  **VLAN 3**: MOC Intranet
  * HaaS API servers and VMs are on here.
  * Users can access through the SSH gateway (see below)
  * **DNS** : 172.16.10.5, 172.16.10.3 172.16.10.4
  * **Default gateway** : 172.16.10.5
  * **Subnet** 172.16.10.0/23
    * **172.16.10.2** [moc-haas-master.bu.edu](PRB-MOC-Haas-Master-Config.html)
    * **172.16.10.6** haas-beta.prb.massopencloud.org
    * **172.16.10.7** moc-haas-deploy.bu.edu
    * **172.16.10.8** hack-n-hil.prb.massopencloud.org
    * **172.16.10.20** BMI-Development VM
    * **172.16.10.21** BMI VM for secure cloud
    * **172.16.10.22** sc-trusted.prb.massopencloud.org (Secure Cloud - Trusted Zone)
    * **172.16.10.23** sc-airlock.prb.masopencloud.org (Secure Cloud - Air Lock)
    * **172.16.10.30** BMI VM for Dan
    * **172.16.10.90** pronto-1-netex.prb.massopencloud.org
    * **172.16.10.91** pronto-2-netex.prb.massopencloud.org
    * **172.16.10.92** pronto-3-netex.prb.massopencloud.org
    * **172.16.10.93** deathstar.prb.massopencloud.org
    * **172.16.10.98** dhcp-vm (DHCP server)
    * **172.16.10.99** bmi-ssh-gateway.prb.massopencloud.org BMI / Secure Cloud SSH gateway (VM on moc-haas-master.bu.edu)
      * accessible from Internet by sshing to moc-haas-master.bu.edu port 22223
      * Can use this snippet in one's `~/.ssh/config` file to make connections easy:
	```
	Host prb-bmi-gateway
	    Hostname moc-haas-master.bu.edu
	    Port 22223
	```
    * **172.16.10.100** ssh-gateway.prb.massopencloud.org (VM on moc-haas-master.bu.edu)
      * accessible from Internet by sshing to moc-haas-master.bu.edu port 22222
	```
	Host prb-gateway
	    Hostname moc-haas-master.bu.edu
        Port 22222
	```
    * **172.16.10.101** Ceph for BMI-Development
    * **172.16.10.207** Red Hat Ceph Storage 3.0 ([details](Red-Hat-Ceph-3.0-cluster.html))
    * **172.16.10.[102-180]** Static IPs available to physical haas nodes based on node number.
      * The lowest octet is `<node num> + 100`. So sun-10 would be 172.16.10.110
    * **172.16.10.[190-220]** DHCP (served from dhcp-vm)
    * **172.16.10.209** intel-51
    * **172.16.10.215** intel-50
 * **VLAN 4** : ceph storage network (managed by BMI team/Ravi)
 * **Subnet** : 10.20.0.0/24. Hosts:
* **VLAN 5** : IPMI interfaces
 * **Subnet** : 10.10.0.0/24. Hosts:
   * IPMI interfaces for all nodes
   * **10.10.0.5** : moc-haas-master.bu.edu (host int)
   * **10.10.0.6** : haas-beta.prb.massopencloud.org
   * **10.10.0.7** : moc-haas-deploy.bu.edu
   * **10.10.0.{10-25}** : sun-{10-25}
       * Note: IPMI of 42 is broken
   * **10.10.0.{36-46}** : super-{36-46}
   * **10.10.0.50** : intel-50
   * **10.10.0.110** : HIL VM for BMI-Development.
   * **10.10.0.149** : dhcp-vm (DHCP server)
   * **10.10.0.[150-200]** : DHCP (served from dhcp-vm)

* **VLAN 6** : External IPMI. This network is for sharing IPMI of certain systems with external users (like the lenovo and moc01 with our Secure Cloud/BMI collaborators)
 * **Subnet** : 10.10.1.0/24
    * **10.10.1.2** moc-haas-master.bu.edu
    * **10.10.1.6** moc-haas-deploy.bu.edu
    * **10.10.1.58** moc01 aka dell-58 IPMI
    * **10.10.1.60** len-60-ipmi.prb.massopencloud.org
    * **10.10.1.61** len-61-ipmi.prb.massopencloud.org
    * **10.10.1.99** bmi-ssh-gateway.prb.massopencloud.org
    * **10.10.1.149** dhcp-vm (DHCP server)
    * **10.10.1.[150-200]** DHCP (served from dhcp-vm)

* **VLAN 900** : BMI provisioing network for secure cloud.
  * **Subnet** : 192.168.39.0/24
     * **92.168.39.1**  hacknhil
     * **192.168.39.2**  BMI VM for dev
     * **192.168.39.3**  BMI VM for secure cloud
     * **192.168.39.4**  BMI VM for Dan

* **VLAN 1000** : BMI provisioning network
  * **Subnet** : 192.168.29.0/24
  * **Ubuntu node provisioned** : 192.168.29.33
  * **DHCP range used** :21-50
  * **192.168.29.99** : bmi-ssh-gateway.prb.massopencloud.org

* **VLAN 1001** : Secure Cloud Airlock
  * **IP range** : 192.168.21.0-63/25 (DHCP IPs 192.168.21.10-63; timeout 1 hr)
  * **192.168.21.2** : sc-airlock.prb.massopencloud.org (on hack-n-hil)

* **VLAN 1002** : Secure Cloud Trusted Zone
  * **IP range** : 192.168.21.128-254/25 (DHCP 192.168.21.128-255)
  * **192.168.21.129** : sc-airlock.prb.massopencloud.org (on hack-n-hil)
  * **192.168.21.130** : sc-trusted.prb.massopencloud.org (on hack-n-hil)

* **VLAN 1003-1500** : haas-beta

* **VLAN 1501-2000** : moc-haas-deploy

* **VLAN 2001-2500** : DEV/moc-haas-master)

******

### IPMI Credentials
  
  **For moc01 aka dell-58 IPMI** :
  * **IPMI username/password** : sc / BrotherHitsCrookTown
  * **Disk-installed OS creds** (ubuntu 16.04): user / password

  **For Lenovos (len-60, len-61) server IPMI** :
  * IPMI username/password : admin/BrotherHitsCrookTown
  * Console-only access credentials: console/MeansAspireStroll

  **For lenovo 62** :
  * **IPMI username/password** : USERID/PASSW0RD

  **For super-{43-46}** :
  * **user** : admin (for supermicros), '' (for suns)
  * **password** : Xxu46RCjtxiLdc
  * credentials for super-42 are probably still default

  **For Cisco nodes in our control (cisco-200 and cisco-204)**
  * **IPMI username/password** : admin/Password1

```bash
for i in `seq 10 25`;
do
	IP=10.10.0.$i;
	echo $IP;
	ssh -t admin@$IP ipmi enable channel lan;
done
```

  **For reference** :
  * **Factory default user/password for the supermicros** : ADMIN/ADMIN
  * [Supermicro IPMI Manual]<!--(http://www.supermicro.com/manuals/other/SMT_IPMI_Manual.pdf)-->

******
### Physical Switches

  **dell-0 (beta) - core router (192.168.3.245)**
  * PowerConnect 5524
  * **Pattern** :
    * Trunked/uplink connections are towards the left ports
    * Node connections are towards the right ports

| Port | Cable Label | Other End | Which haas (beta, deployment or dev)
---- | ---- | ---- | ----
1 | -| - | -
3 | Internet | Internet uplink |
4 | tp-0 | tp-0 switch in non-haas rack / All VLANs trunked |
5 | - | len-61 eth0
6 | - | len-62 eth0 | Secure Cloud
13 | 0x13 | super-38 | beta
14 | 0x14 | super-39 | Kristi (port shutdown)
15 | 0x15 | super-37 | beta
22 | 0x23 | super-36 (haas-beta.prb.massopencloud.org) | beta (haas master)
23 | - | cisco-1 Admin Interface Port | -
24 | - | len-60 nic01 | Secure Cloud
25 te1/0/1 (10G) | - | dell-1/25 ALL VLANs trunked |
26 te1/0/2 (10G) | - | dell-2/25 ALL VLANs trunked |

  **dell-1 (deployment) (192.168.3.246)**
  * PowerConnect 5524
  * **Pattern** :
    * Trunked connections in right half of ports (lower numbers)
    * Node connections in left half of ports (upper numbers)
    * IPMI in top half of ports (odd)
    * eth0 in bottom half of ports (even)

| Port | Cable Label | Other End | Which haas (beta, deployment or dev)
---- | ---- | ---- | ----
1 | - | uplink to NetEx pronto-3 SDN/intranet access mode | NetEx
2 | - | cisco-1 MGMT port  / ALL VLANs TRUNKED | -
3 | - | hack-n-hil nic 2 | -
4 | - | uplink to NetEx pronto-1 SDN/intranet access mode | NetEx
5 | - | - | -
6 | - | - | -
7 | - | - | -
8 | - | uplink to NetEx pronto-2 SDN/intranet access mode | NetEx
9 | - | dell-3 management port |
10 | - | dell-3 port 48 | ALL VLANs trunked
11 | - | - |
12 | 0x25 | super-44 / moc-haas-deploy.bu.edu eno2 (trunked to intranet, internet, ipmi and switch VLANs) | deployment (haas master)
13 | 0x1b | super-46 IPMI | dev
14 | 0x0A | super-44 eno1 | moc-haas-deploy internet
15 | 0x1e | super-45 IPMI | dev
16 | 0x12 | super-43 eth0 | deployment
17 | 0x1A | super-44 IPMI | moc-haas-deploy IPMI
18 | 0x18 | super-42 eth0 | deployment
19 | 0x0b | super-43 IPMI | deployment
20 | 0x1F | super-46 eth0 | deployment
21 | 0x16 | super-42 IPMI | IPMI interface is faulty
22 | 0x08 | super-45 eth0 | deployment
23 | 0x15 | super-41 IPMI | deployment
24 |  |  |
25 te1/0/1 (10G) | - | dell-0/25 / ALL VLANs trunked |
26 te1/0/2 (10G) | -  | dell-3 port 49 | ALL VLANs trunked

  **dell-2 (dev) (192.168.3.247)**
  * PowerConnect 5524
  * **Pattern** :
    * Trunked connections in right half of ports (lower numbers)
    * Node connections in left half of ports (upper numbers)
    * IPMI in top half of ports (odd)
    * eth0 in bottom half of ports (even)

| Port | Cable Label | Other End | Which haas (beta, deployment or dev)
---- | ---- | ---- | ----
1 | - | - | -
2 | - | - | -
3 | - | hack-n-hil/intranet-vlan | hack-n-hil (seems to be disconnected)
4 | - | hack-n-hil em4/ALL VLANs trunked | hack-n-hil
5 | - | -
6 | - | len-62 IPMI | Secure Cloud
7 | - | |
8 | - | |
9 | - | len-61 IPMI | Secure Cloud
10 | - |  |
11 | - | -
12 | - |  |
13 | 0x07 | super-36 (beta haas master) IPMI |
14 | - | super-41 | beta
15 | 0x0D | super-37 IPMI | beta
16 |  | |
17 | 0x0E | super-38 IPMI | beta
18 | 0x11 | super-40 | free
19 | 0x0F | super-39 IPMI | beta
20 | - | - | -
21 | - |  |
22 | - | - | -
23 | - | |
24 | - |  |
25 te1/0/1 (10G) | - | dell-0/26 / ALL VLANs trunked | -

  **cisco-1 (main rack) (192.168.3.230)**
  * Cisco Nexus 3548p-10g
  * Powered off and disconnected

  **tp-0 (other rack) (192.168.3.244)**
  * TL-SG3216
  * **Pattern** :
    * Trunked connections in left half of ports (lower numbers)
    * Node connections in right half of ports (upper numbers)

| Port | Cable Label | Other End | Which haas (beta, deployment or dev)
---- | ---- | ---- | ----
1 | 0x1c | dell-0 port 4/ All VLANs Trunked | -
2 | - | -
3 | - |  |
4 | - | -
5 | - |  |
6 | - | len-60 ipmi 10.10.1.60 | secure cloud
7 | - | think-67 | secure coud
8 | - | -
9 | - |  |
10 | - | len-60 primary interface eno1 | secure cloud
11 | green | moc-control ipmi 10.10.1.58 | PRB gateway
12 | - | - | -
13 | -  | moc-control - port 0 (VLAN 1,5,6)  | PRB gateway
14 |  |  | -
15 | - | - | -
16 |  | moc02 nic4 | -

  **Unmanaged TP-Link (no ip)**
  * TL-SG1016
  * Unmanaged switch that is labelled "internet".
  * **Has connections to** :
    * port 1 on moc-control
    * another connection to moc-02

  **dell-3 (192.168.3.250)**
  * Dell S3048-ON

| Port | Cable Label | Other End | Which haas (beta, deployment or dev)
---- | ---- | ---- | ----
Admin Interface (Ethernet) | - | dell-1 port-9  | -
1 | - | intel-50 nic1 (enp3s0f0) | beta
2 | - | intel-51 nic1 (enp3s0f0) | beta
7 | - | cisco-200 IPMI | not in HIL
8 | - | cisco-204 IPMI | not in HIL
17 | - | intel-50 nic2 (enp3s0f1)| beta
18 | - | intel-51 nic2 (enp3s0f1)| beta
23 | - | cisco-200 (enp4s0f1) | not in HIL
24 | - | cisco-204 (enp4s0f1) | not in HIL
33 | - | intel-50 ipmi | beta
34 | - | intel-51 ipmi | beta
49 (10G) | - | dell-1 26(10G) | ALL VLANs trunked

### Equipment
We have a rack in the PRB server room with hardware for development work on HaaS. Here's what's up there:
* **3 Dell Powerconnect 5524 switches** (24 ports each).
 * The **administrative interface** for these switches are listening on VLAN 1 to IP address 192.168.3.[245-247] using the "admin" username. These are referred to as dell-0 through dell-2, and are labeled.
 * **Password** for the dell switches is the standard MOC
 * [CLI Manual](powerconnect5500_cliref_en-us.pdf). [Dell's support website](http://www.dell.com/support/home/us/en/4/product-support/product/powerconnect-5524/manuals)
* **8 servers based on Supermicro's [SuperServer 5018A-MLTN4][1]**. These have [Super A1SAM-2550F](http://www.supermicro.com/products/motherboard/Atom/X10/A1SAM-2550F.cfm) motherboards. We added 16GiB of memory and a 750GB SATA disk (pulled from the ATLAS array). These are x86_64 Atom processors with VT and IPMI 2.0.
 * IPMI IP addresses are: 10.10.0.[40-47]
* **2 Intel nodes**. They will probably be reunited with their family in engage1, but for now we can use them. Each node has two 14 core Xeons and 256 GB memory.
 * **IPMI addresses are**: 10.10.0.[50-51]
 * **IPMIusername:password**: root:calvin
* **A Dell S3048-ON** switch running Dell OS 9. It belongs to MOC. It's in the middle of the rack and connected to dell-0 switch (the hub).
Use this switch for connecting hardware in the middle rack so we don't have multiple long wires running accross racks.
 * **credentials** : admin/brocadePRB
 * **management IP** : 192.168.4.250 (Vlan 20) - The management port has issues, configure any port to be on that vlan.
 * this switch is a little weird (it's vlan centric) so please read the manual before you try to configure it (or talk to Naved).

[1]: http://www.supermicro.com/products/system/1U/5018/SYS-5018A-MLTN4.cfm

