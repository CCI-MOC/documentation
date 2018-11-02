# Intro

This is an introduction to how Ceilometer works in our production environment as of July 20, 2016

# Ceilometer

Ceilometer consists of three main parts:
**1. The Ceilometer Agents**
**2. A Ceilometer Server**
**3. The MongoDB database that stores Ceilometer Info**

### 1.  Ceilometer Agents

The Ceilometer compute agents are installed on the compute nodes in our production environment. They poll the compute node (using the ceilometer API) that they are installed on for data and send it to the server. The services that run on the compute node are:
openstack-ceilometer-api
openstack-ceilometer-compute
They should be functioning correctly. You can view the logs are /var/log/ceilometer.

### 2. Ceilometer Server

The Ceilometer server lives on the controller node. It collects the data that is being sent from the compute nodes and sends that data to the database. It also collects some data from itself, if configured, such as data on glance images. 
We are only currently interested in data from the compute nodes and their VMs. To this end, we have disabled polling of swift object storage, as it was causing errors. Some other services may still be configured, such as glance images, but can be ignored.

The Ceilometer Server needs a few ceilometer services to be running:
Openstack-ceilometer-alarm-evaluator
Openstack-ceilometer-alaarm-notifier
Openstack-ceilometer-api
Openstack-ceilometer-central
Openstack-ceilometer-collector
Openstack-ceilometer-notification

There is currently a bug where some of the data being sent from compute nodes is discarded. Redhat has opened a bug report on it because it may be a bug: https://bugzilla.redhat.com/show_bug.cgi?id=1358005
When this is resolved, all of the data should be sent from the compute nodes to the ceilometer server on the controller node.
To check if there are any errors, check the /var/log/ceilometer logs. Collector log is probably the most useful. 
Also, note that in the central log you should see a warning about ceilometer neutron client not found. This is expected, as we did not configure the neutron client.

The ceilometer server sends its data that it collects to a database. The database we use currently is MongoDB. This database is located on a VM at the ip address 129.10.5.56.

### MongoDB Database

The MongoDB Database is located at 129.10.5.56. It stores data passed to it from ceilometer, in the ceilometer db. Then, our sensu server runs a check on this VM that queries MongoDB for it's data and sends that to our production InfluxDB. We do not have checks to send all data to InfluxDB at this time, and more checks need to be written.