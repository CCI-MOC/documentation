# Setting Up Tempest
These steps are after you clone the tempest-VM and try to configure your environment.

### Installation
1. Create a public network on OpenStack.
  * Login as admin. Then go to Admin->Networks->Create Network
  * Give some name, select project as "admin", provider network type as "flat" and select "external network". Leave other things as it is.
  * Once its created, click on the network and add a subnet to it. Use any subnet range you want.
2. Create a demo tenant and demo user with some password.
3. Edit <dir-path>/etc/tempest.conf file:
```
[auth]
default_credentials_domain_name=<your region name>
# Source /root/keystonerc_admin
# openstack endpoint list   <--- Get the region-name from here
admin_tenant_name=<your admin username> 
admin_password=<your admin password 
# Get admin username and password from /root/keystonerc_admin on controller node.

[compute]
image_ref = <cirros image id>
image_ref_alt = <cirros image id>
# glance image-list <----- get the id of cirros image
region = <your region name>

[dashboard]
url = <controller node url>

[identity]
url = <controller node uri>
region = <your region name>

[network]
region = <your region name>
public_network_id = <id of public network
# neutron net-list <---- get the id of network configured as public
floating_network_name = <id of floating ip network>
# neutron net-list <---- get the name of network configured as public
```

### Running Tests
```[root@tempest-xyz tempest]# ./run_tempest.sh -t tempest.api```

If you get any errors like its unable to find keystone.staging.moc.edu or any DNS resolution error, add this to /etc/hosts:
```<your-controller-ip> <dns-name1> <dns-name2> <dns-name3> ...```

**Example** :
``10.14.37.207 compute-207.staging.moc.edu keystone.staging.moc.edu nova.staging.moc.edu glance.staging.moc.edu neutron.staging.moc.edu cinder.staging.moc.edu neutron.staging.moc.edu```

