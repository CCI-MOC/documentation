#### Steps for upgrade of controller node

###### Before upgrade:-
Create an osp8.repo file for latest packages
```
controller# vi osp8.repo

[rhel-7-server-openstack-8-rpms]
name=rhel-7-server-openstack-8-rpms
baseurl=http://10.13.37.254/repos/rhel-7-server-openstack-8-rpms
enabled=1
gpgcheck=0
```
Next, update the controller
```
controller# mv osp8.repo /etc/yum.repos.d/
controller# yum -y update
controller# reboot
```

Next, checkout liberty branch of puppet
```
foreman# git checkout liberty
```

Next, follow these steps after reboot
```
controller# keystone-manage db_sync
controller# nova-manage db sync
controller# glance-manage db sync
controller# cinder-manage db sync
controller# heat-manage db_sync
controller# neutron-db-manage --config-file /etc/neutron/neutron.conf --config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade liberty
controller# puppet agent -t
controller# openstack-service restart
```

#### Steps for upgrade of compute node
**NOTE:** Needs to be performed after upgrade of controller

###### Before upgrade:-
```
compute# vi osp8.repo

[rhel-7-server-openstack-8-rpms]
name=rhel-7-server-openstack-8-rpms
baseurl=http://10.13.37.254/repos/rhel-7-server-openstack-8-rpms
enabled=1
gpgcheck=0
```

Next, upgrade the compute node
```
compute# mv osp8.repo /etc/yum.repos.d/
compute# yum -y update
compute# reboot
```

Repuppetize the nodes
```
compute# puppet agent -t
compute# openstack-service restart
```