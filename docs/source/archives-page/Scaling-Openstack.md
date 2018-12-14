# OUT OF DATE!!!

For the openstack part: We're morphing Enterprise Openstack to be Carrier-grade Openstack (quote from Peter)


# Networking
## Spanning networks
Redhat RHEV can currently span networks between RHEV clusters and openstack clusters by sharing a neutron instance.

* **MOC v1**: using multiple nova regions that share a keystone backend and a single neutron.
* **MOC v2**: Some sort of horizon, non-trusting protocol that can span providers.

## Scaling
### Neutron/GRE has a single point of failure
**Distributing the neutron management would be key to a large open source deployment.**
Possible solutions:
* Creating a bridge in KVM on each of the nodes that would allow un-proxied Internet access
 * Creating a non-neutron-managed VM that's connected between the private and the VLAN that's public

* All external traffic (using the software router) travels through a single node.
 * **S3/lustre/other guest-based network traffic will have to travel through that single router**
 * Manila is a project to export a filesystem to a guest VM from the host (thus: could use the compute node's networking to get around neutron limitation) 
* Peer to peer traffic is only coordinated centrally; packets flow between peers
* May not be an issue for 10s of nodes
* Even with the Neutron Node in active/passive mode for reliability, if the main node goes down, the cluster dies as the routes are lost
* Openstack does not currently let you mix modes between Neutron (GRE) and Nova-network (VLAN-based) within a single cluster.
  
### Hardware as a networking solution
* VxLANs supports 24 bits of network (similar to VLANs)

# Storage
Plan is to use glustre.

# Chargeback
Can use CloudForms for billing. (Redhat product, but plans are to opensource it).

# Upgrades/Running the latest
**Openstack does not have a smooth upgrade path that preserves configuration and VMs.**

# Managing openstack
* MOC, if we wanted to run on the latest, will need to help refine the Continuous Integration to define known-good snapshots between the various components.
 * Example: Currently, ping tests are done in CI, but bandwidth tests are not (a neutron patch recently broke compatibility).
* MOC could have a testbed known to be experimental. After a week, it could be declared good. 
