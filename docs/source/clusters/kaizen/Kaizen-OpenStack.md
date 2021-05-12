This document briefly describes the hardware running Kaizen OpenStack cluster as of May 12, 2021.

## Openstack Cluster

Our openstack cluster consists of:

* 24 Computes nodes + 2 GPU nodes
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
| RAM | 768 GB DDR3 |
| Model | PowerEdge R620 |
| Networking | Dual 10GB Nics |

## Switches

We use 8 Brocade VDX 6740 switches and 2 Cisco Nexus 6000 series switches for our Kaizen cluster.
