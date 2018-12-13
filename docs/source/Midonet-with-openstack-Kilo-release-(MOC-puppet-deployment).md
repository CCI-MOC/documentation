First, you need to deploy openstack-kilo release with 1 controller and 'n' compute nodes. Make sure that the openstack environment is fully functional. This deployment has been tested with openstack deployment in Kaizen staging environment and it works fine. Once you have full openstack deployment, run these scripts on controller and compute nodes and have midonet deployed automatically.

* [Github Link](https://github.com/rahulait/midonet-install)

#### Deployment design
* Gateway residing on the controller node
* Cassandra database on the controller node
* Zookeeper on the controller node
* Midolman agents installed on controller and compute nodes

#### On controller Node
Run These scripts in order. Few of them might reboot the controller, but thats ok.

1. [Add Midonet repositories and update](https://github.com/rahulait/midonet-install/blob/master/controller/install1.sh)
2. [Remove openvswitch, install zookeeper and cassandra](https://github.com/rahulait/midonet-install/blob/master/controller/install2.sh)
3. [Install midolman on controller](https://github.com/rahulait/midonet-install/blob/master/controller/install3.sh)
4. ***NOTE:*** Before executing next steps, make sure you have configured all your compute nodes. Next step has dependency on compute nodes and should be run only after compute nodes configuration is done.
5. [Add all controller and compute nodes to tunnel zone](https://github.com/rahulait/midonet-install/blob/master/controller/install4.sh)
6. [Network configuration](https://github.com/rahulait/midonet-install/blob/master/controller/install5.sh)

#### On compute Node
1. [Add midonet repositories and update](https://github.com/rahulait/midonet-install/blob/master/compute/install1.sh)
2. [Install packages and configure compute](https://github.com/rahulait/midonet-install/blob/master/compute/install2.sh)

#### Steps to do after deployment
1. Login to your project.
2. Create a private network, create a router and connect to the public network.
3. Launch a VM.
4. **IMPORTANT**: Make sure you allow ICMP and SSH in security-groups. If you try to ping from the machine to outside world, reverse traffic gets dropped by the security-groups. Make sure you enable it.
