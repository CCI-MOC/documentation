This document briefly describes the hardware running Kaizen OpenStack cluster and Ceph Storage cluster as of January 14, 2020.

## Openstack Cluster

Our openstack cluster consists of:

* 15 Computes nodes + 1 GPU node
	- to be added: 10 Compute nodes + 2 GPU nodes + 2 FPGA nodes
* 3 Controllers
* 2 Network Controllers

Compute nodes configuration:

| Field              | Value    |
| ------------------ | ------ |
| CPU(s) | 40 |
| Thread(s) per core | 2 |
| Core(s) per socket | 10 |
| Socket(s) | 2 |
| Model Name | Intel(R) Xeon(R) CPU E5-2660 v2 @ 2.20GHz |
| CPU MHz | 2644.995 |
| RAM | 384 GB DDR3 |
| Model | PowerEdge R620 |
| Networking | Dual 10GB Nics |


## Ceph

The ceph cluster used for our openstack and baremetal environments is made up of 8 OSD servers.

4 OSD servers are used for "size" root, while the other 4 provide the "performance" root.

Everything is triple replicated except RGWs.

### Size root

10 X 10TB drive per OSD server. This gives us a total of 400 TB of raw storage in the "size" root.

Server configuration:

| Field              | Value    |
| ------------------ | ------ |
| CPU(s) | 12 |
| Thread(s) per core | 2 |
| Core(s) per socket | 6 |
| Socket(s) | 2 |
| Model Name | Intel(R) Xeon(R) CPU E5-2603 v4 @ 1.70GHz |
| CPU MHz | 1700.000 |
| RAM | 128 GB DDR4 |
| Model | PowerEdge R730xd |
| Networking | Dual 10GB Nics |
| Storage | 10 X 10TB drives for OSD |
| Storage | 2 X 128GB drives for OS |
| Storage | 2 X 1.6 TB drives for journal |


### Performance root

Server configuration:

| Field              | Value    |
| ------------------ | ------ |
| CPU(s) | 32 |
| Thread(s) per core | 2 |
| Core(s) per socket | 8 |
| Socket(s) | 2 |
| Model Name | Intel(R) Xeon(R) CPU E5-2640 v2 @ 2.00GHz |
| CPU MHz | 2300.048 |
| RAM | 256 GB DDR3 |
| Model | PRIMERGY RX300 S8 |
| Networking | Dual 10GB Nics |
| Storage | 38 X 1TB drive for OSD |
| Storage | 2 X 1TB drives for OS |

OSD servers for "performance" root:

38 X 1TB drive per OSD server for OSDs. This gives us a total of 152 TB raw storage.

## Switches

We use 8 Brocade VDX 6740 switches and 2 Cisco Nexus 6000 series switches for our Kaizen cluster.
