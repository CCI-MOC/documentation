Testing coverage example for keystone, glance, neutron, and nova:  

```
. keystonerc_admin
glance image-create --name "cirros-0.3.2-x86_64" --disk-format qcow2 --container-format bare –-is-public true --file cirros-0.3.2-i386-disk.img
# Enable ping and SSH in default security group 
neutron security-group-rule-create --protocol icmp --direction ingress default 
neutron security-group-rule-create --protocol tcp --port-range-min 22 --port-range-max 22 --direction ingress default

# Create a SSH keypair
nova keypair-add testkey > /root/testkey.pem 
chmod 600 /root/testkey.pem
neutron net-create pubnet1 --shared --router:external=True 
neutron subnet-create --name pubsubnet1 --dns-nameserver 157.206.117.226 --allocation-pool start=7.255.26.100,end=7.255.26.150 --gateway 7.255.26.1 --disable-dhcp pubnet1 7.255.26.0/24

# Capture IDs 
PUBNETID=$(neutron net-list | grep pubnet1 | awk '{print $2}') 

#Creating Internal L2 private networks 
#Internal-only router to connect multiple L2 networks privately. 
neutron net-create privnet1 
neutron subnet-create --name privsubnet1 privnet1 172.16.25.0/24 

# Capture Net/Subnet UUIDs 
PRIVNET1ID=$(neutron net-list | grep privnet1 | awk '{print $2}') 
PRIVSUBNET1ID=$(neutron subnet-list | grep privsubnet1 | awk '{print $2}') 

# Create router for L3 to connect each network 
neutron router-create router1 
neutron router-interface-add router1 $PRIVSUBNET1ID 

# Set gateway for your external network 
neutron router-gateway-set router1 $PUBNETID 

### Launch an instance... 
CIRROSID=$(glance image-list | grep cirros | awk '{print $2}') 
nova boot testserver --flavor 1 --image $CIRROSID --key-name testkey --security-groups default --nic net-id=$PRIVNET1ID
FLOATINGOUTPUT=$(neutron floatingip-create pubnet1)
FLOATINGIP=$(grep floating_ip_address $FLOATINGOUTPUT | awk '{print $4}'
nova add-floating-ip testserver $FLOATINGIP
# Validate connectivity (and that the guest can ping externally)
ssh -i /root/testkey.pem cirros@$FLOATINGIP 'ping -c 5 8.8.8.8'



Clean up the environment after validation (optional - but we're going to test more below)
. /root/keystonerc_admin
nova delete testserver
FLOATINGIP=$(neutron floatingip-list | grep 10.81.63 | awk '{print $2}')
neutron floatingip-delete $FLOATINGIP
neutron router-gateway-clear router1
PRIVSUBNET1ID=$(neutron subnet-list | grep privsubnet1 | awk '{print $2}') 
neutron router-interface-delete router1 $PRIVSUBNET1ID 
neutron router-delete router1 
neutron subnet-delete privsubnet1 
neutron net-delete privnet1 
neutron subnet-delete pubsubnet1 
neutron net-delete pubnet1 

# NOTE THIS DOES NOT CLEAN UP THE SECURITY RULES.  SO YOU WON'T NEED TO ADD THEM AGAIN

nova keypair-delete testkey
rm -f /root/testkey.pem

Cinder, Ceilometer, Heat basic validation 

# Create 1GB cinder volume 'test3-vol1' and attach to 'rhel6-test2' instance
source ~/keystonerc_user1 
cinder create --display-name test3-vol1 1 
cinder list 
nova list 
nova volume-attach rhel7-test3  auto 
cinder list 

#Setup the cinder volume on 'rhel7-test3' instance
source ~/keystonerc_user1 
ssh -i ~/testkey.pem cloud-user@10.81.63.114
[cloud-user@rhel7-test3 ~]$ sudo su -
[root@rhel7-test3 ~]# fdisk /dev/vdb 
Welcome to fdisk (util-linux 2.23.2). 

Changes will remain in memory only, until you decide to write them. 
Be careful before using the write command. 

Device does not contain a recognized partition table 
Building a new DOS disklabel with disk identifier 0xa185089a. 

Command (m for help): n 
Partition type: 
   p   primary (0 primary, 0 extended, 4 free) 
   e   extended 
Select (default p): p 
Partition number (1-4, default 1): 
First sector (2048-2097151, default 2048): 
Using default value 2048 
Last sector, +sectors or +size{K,M,G} (2048-2097151, default 2097151): 
Using default value 2097151 
Partition 1 of type Linux and of size 1023 MiB is set 

Command (m for help): w 
The partition table has been altered! 
 
Calling ioctl() to re-read partition table. 
Syncing disks. 

[root@rhel7-test3 ~]# mkfs.xfs /dev/vdb1 
meta-data=/dev/vdb1              isize=256    agcount=4, agsize=65472 blks 
         =                       sectsz=512   attr=2, projid32bit=1 
         =                       crc=0 
data     =                       bsize=4096   blocks=261888, imaxpct=25 
         =                       sunit=0      swidth=0 blks 
naming   =version 2              bsize=4096   ascii-ci=0 ftype=0 
log      =internal log           bsize=4096   blocks=853, version=2 
         =                       sectsz=512   sunit=0 blks, lazy-count=1 
realtime =none                   extsz=4096   blocks=0, rtextents=0 
[root@rhel7-test3 ~]# mkdir /vol1 
[root@rhel7-test3 ~]# mount /dev/vdb1 /vol1 
[root@rhel7-test3 /]# df -h /vol1 
Filesystem      Size  Used Avail Use% Mounted on 
/dev/vdb1      1020M   33M  988M   4% /vol1 

# Create cinder snapshot of volume 'test3-vol1'
source ~/keystonerc_user1 
cinder snapshot-create --display-name test3-vol1-snap1 test3-vol1 --force true 
cinder snapshot-list

# Clean up cinder volumes and snapshots
cinder snapshot-delete test3-vol1-snap1
nova volume-detach rhel7-test3 dcebde67-6ccf-4d29-a997-09dde6a268c6
cinder delete test3-vol1

# Sample Ceilometer data
source ~/keystonerc_user1 
ceilometer sample-list -m image 
ceilometer statistics -m image 

# Orchestrate WordPress install using a Heat template 'Wordpress_Single_Instance.yaml'
cat ~/Wordpress_Single_Instance.yaml  (Attached to this email - I think it works but it has been a while... ) 

# Create the stack
heat stack-create -f ~/Wordpress_Single_Instance.yaml -P \
 FloatingNetworkUUID=89725f98-330b-4560-b605-574529329a79 -P \
KeyName=testkey -P DBrootpassword=password wordpress

# Validate the stack. 'stack_status' should be CREATE_COMPLETE
heat stack-list
heat event-list wordpress
heat stack-show wordpress
# Find the output value and connect with your web browser.  You should see the initial Wordpress Admin page.

# Delete your stack:
heat stack-delete
```