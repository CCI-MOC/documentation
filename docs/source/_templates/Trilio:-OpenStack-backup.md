
Status: 

(3/30) Trilio have changed their game plan and is now working on making Trilio to work with Pike! Expected release is April. This change in strategy came about after an internal re-evaluation on the need of their customer base. It appears their customers have started to recently demand for the Pike release. With this and with the push from the MOC end to spupport Pike, they decided to put Pike release as a higher priority. 

(2/28) We are running into issues with Pike and the current version of Trilio Vault. The current of Trilio release which we have been using is based on qualification of Ocata. Both Kaizen and Engage1 have Pike release of OpenStack. We are working on the solution at the moment. 
# Background

Trilio was identified as the software for the VM backup solution in production Kaizen especially. BU research IS&T also helped to provide initial storage for this effort. 

# NFS server mount points 
Related information on the storage provided by BU research IS&T is as followed

    nfs-isinas.bu.edu:/ifs/archive/ou/moc-archive/archive
  
    Your share has the following quota configured:
    Type AppliesTo Path Snap Hard Soft Adv Used
    -----------------------------------------------------------------------------------------
    directory DEFAULT /ifs/noreplica/ou/moc-archive/archive No 38.50T 35.00T - 2.0k
    -----------------------------------------------------------------------------------------
 
    1. The "Soft" quota is the amount of storage owned by the client. (This is the amount provisioned to and/or purchased by the client.)
    2. The "Hard" quota is 110% of the "Soft Quota". The client is allowed to exceed the Soft quota up to the hard quota amount for up to 7 days. After 7 days, if the quota usage remains above the soft quota, writes will be disabled. Notification email will be send daily when either soft/hard quota is exceeded.
 
    Your share has the following access permissions configured:
    Permissions:
    Paths: /ifs/noreplica/ou/moc-archive/archive
    Root Clients: 128.31.22.192, 128.31.22.194, 128.31.22.195 

# Three Node Openstack Cluster (one controller and two computes) 

    Make sure to have three clean instances that are running RHEL 7 auto-subscribing.

    Ensure that the host-names of these three instances have no underscores or other weird characters. This sometimes creates DNS issues when installing PackStack.

    Ensure that the /etc/hosts/ file of the controller node has entries for it's own IP address as well as entries for the two other IP addresses of the two compute nodes.

    In addition to having entries, make sure that all three hosts are named with a fully-qualified domain name.

    Allocate enough resources to the controller instance. The RDO website advises using a VM with 16 GB of RAM. 

    Make sure all instances are accessible via SSH from the root user on the controller.

An example for more clarity:

   192.168.100.12 (controller), 192.168.100.14 (compute), 192.168.100.4 (compute).

   Hostnames are controller.moclocal, compute1.moclocal, compute2.moclocal respectively.
  
  hosts file:

    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4

    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

    192.168.100.12 controller.moclocal localhost.localdomain localhost4 localhost4.localdomain4 controller

    192.168.100.14 compute1.moclocal compute1

    192.168.100.4 compute2.moclocal compute2

Perform the following commands on each node:

    systemctl disable firewalld
    systemctl stop firewalld
    systemctl stop NetworkManager
    systemctl disable NetworkManager
    subscription-manager repos --disable=*
    subscription-manager repos --enable=rhel-7-server-rpms
    subscription-manager repos --enable=rhel-7-server-rh-common-rpms
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    subscription-manager repos --enable rhel-7-server-optional-rpms
    yum install -y yum-utils
    yum update -y
    systemctl reboot

Perform the following commands on the controller node:

    sudo yum install -y https://www.rdoproject.org/repos/rdo-release.rpm
    sudo yum update -y
    sudo yum install -y openstack-packstack
    packstack --install-hosts=CONTROLLER_ADDRESS,COMUPUTE_ADDRESS1,COMPUTE_ADDRESS2, filling in the appropriate IP addresses for each of your nodes. 

# Setting up and configuring Trilio Vault and it's other components. 
http://144.217.78.104:8000/deployment.html

   The documentation does a good job of explaining how to set all the Triio components up. Follow the above link.
   Make sure to follow the instructions to set Trilio up as a separate instance.


![](<a href="https://ibb.co/hKW1F7"><img src="https://preview.ibb.co/gnvTv7/Screenshot_from_2018_02_26_15_10_07.png" alt="Screenshot_from_2018_02_26_15_10_07" border="0"></a>)
 
