## Ceph Cluster Updates to Quickstart Guide 
Refer to the ceph quick start guide for the preflight checklist and then storage cluster quick start and make changes mentioned here:
 -  [Quick Start Preflight](http://docs.ceph.com/docs/master/start/quick-start-preflight/)
 -  [Quick Ceph Deploy](http://docs.ceph.com/docs/master/start/quick-ceph-deploy/)

*Note: Might not need the initial 2 steps for subscription manager and EPEL repo.*

 1. Update yum before updating/ installing other packages. 
    If you don't get the latest version of ceph-deploy (2.0), use this method to update it to the latest version of 1.5. 
    I got 1.5.37 by default but upgraded it to 1.5.39 to solve a few issues while creating OSDs.
    ```shell  
    wget http://download.ceph.com/rpm-luminous/el7/noarch/ceph-deploy-1.5.39-0.noarch.rpm
    rpm -Uvh ceph-deploy-1.5.39-0.noarch.rpm --nodeps
    ```
 1. Make sure to update NTP services and change time zone to EST on all nodes including admin.
    ```shell
    ssh user@ceph-server
    sudo useradd -d /home/{username} -m {username}
    sudo passwd {username}
    ```
     -  Replace "user" with the OS name of node like centos and ceph-server with IP address of that node.
     -  Replace "username" with the OS name of node like centos.
 1. Add your user name to sudoers file
    ```shell
    echo "{username} ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/{username}
    sudo chmod 0440 /etc/sudoers.d/{username}
    ```
    Replace "username" with OS name on the node like centos.
 1. For the ssh config file:
    ```shell
    Host node1_name
       Hostname node1_ip_address
       User {username}
    Host node2_name
       Hostname node2_ip_address
       User {username}
    Host node3_name
       Hostname node3_ip_address
       User {username}
    ```

**Replace username with OS name of node like centos.**
 1. Either enable firewall on all nodes OR use iptables.
 1. Create new volume in Kaizen for each node (excluding admin) and attach it to the nodes to create a new disk 
 for each node (reflected as `/dev/vdb` in the path). 
 The volume size might depend on the node size. One of the forums said if the node size is 5-10 GB then the volume should be around 20 GB.
 1. For adding a new node after the cluster has been formed install ceph packages just for that node.
    ```shell
    ceph-deploy install new_node_name
    ```
    Transfer the admin-client keyring 
    ```shell
    ceph-deploy admin new_node_name
    ```
 1. Depending on the version of ceph-deploy the OSD creation commands might differ. 
    The version 2.0 and above is mentioned in the document. For version 1.5.39 the command is:
    ```shell
    ceph-deploy osd create {node_name}:/dev/vdb
    ```
    If this doesn't work, you can split this command into two:- prepare and activate. Do this from the admin node.
    ```shell
    ceph-deploy osd prepare {node_name}:/dev/vdb
    ceph-deploy osd activate {node_name}:/dev/vdb
    ```
 1. If you run into an error asking for a few missing keyrings like this:
    ```shell
    2018-03-20 12:16:59.550708 7f916621c700 -1 auth: unable to find a keyring on /etc/ceph/ceph.client.admin.keyring,/etc/ceph/ceph.keyring,/etc/ceph/keyring,/etc/ceph/keyr    ing.bin: (2) No such file or directory
    2018-03-20 12:16:59.550718 7f916621c700 -1 monclient(hunting): ERROR: missing keyring, cannot use cephx for authentication
    2018-03-20 12:16:59.550720 7f916621c700  0 librados: client.admin initialization error (2) No such file or directory
    Error connecting to cluster: ObjectNotFound
    ```
    Change permissions for the ceph.client.admin.keyring in all nodes including admin to make them readable/ writable. 
    ```shell
    sudo chmod 644 /etc/ceph/ceph.client.admin.keyring
    ```
    You are NOT missing/ need any other keyrings apart from the ones already created and listed in the document.
 1. You might run into a health error if you've deployed only one OSD and the monitor on the same node. 
    I believe the monitor is just redundant on one node. Make sure to deploy the other OSDs before checking the health status.
 1. You will get a health error/ warning if an OSD goes down. You need to reactivate it. 
    SSH into it and write: `sudo ceph-disk activate-all` to activate all placement groups. 
    Change keyring permission again for the node and it will be up and running.
 1. To create a new OSD pool: `ceph osd pool create {pool-name} pg_num`
    It is mandatory to choose the value of pg_num because it cannot be calculated automatically. Here are a few values commonly used:
     -  Less than 5 OSDs set pg_num to 128
     -  Between 5 and 10 OSDs set pg_num to 512
     -  Between 10 and 50 OSDs set pg_num to 1024
     -  If you have more than 50 OSDs, you need to understand the tradeoffs and how to calculate the pg_num value by yourself
     -  For calculating pg_num value by yourself please take help of pgcalc tool

To set the object name
```shell
ceph osd map {poolname} {object-name}
```
