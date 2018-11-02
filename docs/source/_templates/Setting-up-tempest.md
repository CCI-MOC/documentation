## Setup
```
These steps are after you clone the tempest-VM and try to configure 
your environment.

Step1:-
Create a public network on openstack.
* Login as admin. Then goto Admin->Networks->Create Network
* Give some name, select project as "admin", provider network type 
as "flat" and select "external network". Leave other things as it is.
* Once its created, click on the network and add a subnet to it. 
Use any subnet range you want.

Step2:-
Create a demo tenant and demo user with some password.


Modifying the <dir-path>/etc/tempest.conf file:-

[auth]
1. default_credentials_domain_name
Set it to your region name.
source /root/keystonerc_admin
openstack endpoint list   <--- Get the region-name from here

2. Set the admin_tenant_name and admin_password.
Get them from /root/keystonerc_admin on controller node.

[compute]
3. Set the image_ref and image_ref_alt parameter
Get it from controller node.
glance image-list <----- get the id of cirros image

4. Set the region to the one set in step 1.

[dashboard]
5. Change the URL to point to your controller node

[identity]
6. Set uri to your controller

7. Set region to your region-name

[network]
8. Set region to your region-name

9. Set the public_network_id
neutron net-list <---- get the id of network configured as public

10. Set the floating_network_name
neutron net-list <---- get the name of network configured as public
```

## Running Tests
```
[root@tempest-xyz tempest]# ./run_tempest.sh -t tempest.api

If you get any errors like its unable to find keystone.staging.moc.edu or 
any DNS resolution error, add this to /etc/hosts
<your-controller-ip> <dns-name1> <dns-name2> <dns-name3> ...
Example:
10.14.37.207 compute-207.staging.moc.edu keystone.staging.moc.edu nova.staging.moc.edu glance.staging.moc.edu neutron.staging.moc.edu cinder.staging.moc.edu neutron.staging.moc.edu
```