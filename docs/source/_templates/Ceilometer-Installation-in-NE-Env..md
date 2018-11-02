# Ceilometer Introduction
To learn more about Ceilometer in our production environment, please go here: [[Ceilometer Introduction]]. This will give you a background on how ceilometer is installed that will help you understand this guide.

#Install MongoDB

We have MongoDB installed on a separate node located at 129.10.5.56. Go there and then follow these instructions.
###Install the MongoDB package
```# yum install mongodb-server mongodb```

###Edit the /etc/mongod.conf file and complete the following actions
We should change the bind ip in mongodb. The following is our case:
```
bind_ip = 129.10.5.56
```

We can also change the option smallfiles to true:
```smallfiles = true```

###Start mongodb services and configure them to start when the system boots

```
# systemctl enable mongod.service
# systemctl start mongod.service
```

###Create the ceilometer database in mongodb
```
# mongo --host 129.10.5.56 --eval '
  db = db.getSiblingDB("ceilometer");
  db.createUser({user: "ceilometer",
  pwd: "CEILOMETER_PASS" (replace CEILOMETER_PASS with the ceilometer password from the production hiera file) ,
  roles: [ "readWrite", "dbAdmin" ]})'
```


#Install and Configure Controller Node

### Configuring the Controller Node


### Create the service credentials, complete these steps

1. Source the admin credentials to gain access to admin-only CLI commands

    ```
    # source keystone_admin
    ```


2. Create the ceilometer user


```
$ openstack user create --password-prompt ceilometer
User Password:CEILOMETER_PASS (from hiera file)
Repeat User Password:
```

3. Add the admin role to the ceilometer user

    
```
  $ openstack role add --project services --user ceilometer admin
```

4.Create the ceilometer service entity:


```
$ openstack service create --name ceilometer \
  --description "Telemetry" metering
```

5.Create the Telemetry module API endpoint:

```
$ openstack endpoint create --publicurl http://10.13.37.17:8777 --internalurl http://10.13.37.17:8777 --adminurl http://10.13.37.17:8777 --region MOC_Kaizen metering
```
Above is only an example of the endpoint creation, you should change the url, region and service name accordingly. 


##Install and configure ceilometer module:
###install package:
```
# yum install openstack-ceilometer-api openstack-ceilometer-collector openstack-ceilometer-notification openstack-ceilometer-central openstack-ceilometer-alarm python-ceilometerclient
```


###Edit the /etc/ceilometer/ceilometer.conf file and complete the following actions:

Uncomment these sections and edit them accordingly


1.In the [database] section, configure database access:


```
[database]

...
connection = mongodb://ceilometer:(HIERA_PASSWORD_HERE)@129.10.5.56:27017/ceilometer
```

2.In the [DEFAULT] section, configure RabbitMQ message queue access:

```
[DEFAULT]
...

rabbit_host = compute-215.staging.moc.edu (hostname)
rabbit_port = 5672
rabbit_hosts = compute-215.staging.moc.edu:5672 (hostname + rabbitmq port)
rabbit_use_ssl = false
rabbit_userid = openstack
rabbit_password = (password from hiera file)
rpc_backend = rabbit
```

3.In the [keystone_authtoken] section, configure Identity service access:

```
[keystone_authtoken]

auth_uri = https://keystone.staging.moc.edu:5000 (your keystone, port 5000)
identity_uri = https://keystone.staging.moc.edu:35357 (your keystone, port 35357)
admin_user = ceilometer
admin_password = (password from hiera file)
admin_tenant_name = services
```

4.In the [service_credentials] section, configure service credentials:


```
[service_credentials]
...
os_username = ceilometer
os_password = (password from hiera file)
os_auth_url = https://keystone.staging.moc.edu:35357/v2.0 (your keystone)
os_region_name = MOC_Test(your region name)

```



##Finalize Controller Installation:
###Configure to start ceilometer service at reboot
```
#systemctl enable openstack-ceilometer-api.service openstack-ceilometer-notification.service openstack-ceilometer-central.service openstack-ceilometer-collector.service openstack-ceilometer-alarm-evaluator.service openstack-ceilometer-alarm-notifier.service
```
###To start ceilometer service
```
#systemctl start openstack-ceilometer-api.service openstack-ceilometer-notification.service openstack-ceilometer-central.service openstack-ceilometer-collector.service openstack-ceilometer-alarm-evaluator.service openstack-ceilometer-alarm-notifier.service
```
###To stop ceilometer service
```
#systemctl stop openstack-ceilometer-api.service openstack-ceilometer-notification.service openstack-ceilometer-central.service openstack-ceilometer-collector.service openstack-ceilometer-alarm-evaluator.service openstack-ceilometer-alarm-notifier.service
```

##Log files

Ceilometer log files are all located at  ```/var/log/ceilometer/```, you can check the logs for errors after the finalization part. 

##Websites that are useful:
Ceilometer client api
http://docs.openstack.org/developer/ceilometer/webapi/v2.html

 Ceilometer installation guide for kilo release
http://docs.openstack.org/kilo/install-guide/install/yum/content/ch_ceilometer.html



##Access Ceilometer Data
### CLI Command
1.Source ceilometer environment variables

2.Check all the meters 

```
ceilometer meter-list
```

# Configure Compute Service
##To install and configure the agent

1. Install the packages:
```
# yum install openstack-ceilometer-compute python-ceilometerclient python-pecan
```

2.Edit the /etc/ceilometer/ceilometer.conf file and complete the following actions:
**Note: all of the information in this conf file, such as the rabbit_host, auth_uri, etc., should match the controllers config file**

a. In the [publisher] section, configure the telemetry secret:

```
[publisher]
...
telemetry_secret = TELEMETRY_SECRET
Replace TELEMETRY_SECRET with the telemetry secret you chose for the Telemetry module.
```

b.In the [DEFAULT] and [oslo_messaging_rabbit] sections, configure RabbitMQ message queue access:

```
[DEFAULT]
...
rpc_backend = rabbit
 
[oslo_messaging_rabbit]
...
rabbit_host = 10.13.47.4  
rabbit_userid = openstack
rabbit_password = RABBIT_PASS
```
Replace RABBIT_PASS with the password you chose for the openstack account in RabbitMQ.

c. In the [keystone_authtoken] section, configure Identity service access:

```
[keystone_authtoken]
...
auth_uri = https://129.10.3.47:5000/v2.0
identity_uri = https://129.10.3.47:35357
admin_tenant_name = services
admin_user = ceilometer
admin_password = CEILOMETER_PASS
```
Replace CEILOMETER_PASS with the password you chose for the Telemetry module database. IN our case it is 'welcome1'

d.In the [service_credentials] section, configure service credentials:

```
[service_credentials]
...
os_auth_url = https://129.10.3.47:5000/v2.0
os_username = ceilometer
os_tenant_name = services
os_password = CEILOMETER_PASS
os_endpoint_type = internalURL
os_region_name = RegionOne
```

Replace CEILOMETER_PASS with the password you chose for the ceilometer user in the Identity service.

 
##To configure notifications

1.Configure the Compute service to send notifications to the message bus. Edit the /etc/nova/nova.conf file and configure notifications in the [DEFAULT] section:

```
[DEFAULT]
...
instance_usage_audit = True
instance_usage_audit_period = hour
notify_on_state_change = vm_and_task_state
notification_driver = messagingv2
```


##To finalize installation

1.Start the Telemetry agent and configure it to start when the system boots:

```
# systemctl enable openstack-ceilometer-compute.service
# systemctl start openstack-ceilometer-compute.service
```

2.Restart the Compute service:

```
# systemctl restart openstack-nova-compute.service
```

# Configure IPMI Service
##Install the ipmi package
```
# yum install openstack-ceilometer-ipmi
```
## Install ipmitool
```
# yum install ipmitool
```

## Start impi agent
```
# systemctl enable openstack-ceilometer-ipmi.service
# systemctl start openstack-ceilometer-ipmi.service
```