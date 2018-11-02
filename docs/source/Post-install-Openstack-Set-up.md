#### Create public network through command line:
```
source ~/keystonerc_admin
neutron net-create public-net --router:external --provider:physical_network physnet1 --provider:network_type flat
neutron subnet-create public-net 129.10.3.0/25 --name public --allocation-pool start=129.10.3.60,end=129.10.3.120 --gateway 129.10.3.1