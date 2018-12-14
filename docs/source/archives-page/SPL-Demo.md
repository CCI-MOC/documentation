#SPL Demo


### To get the SPL
```
git clone http://github.com/cci-moc/moc-public.git
```

as root (sudo)
```
export PYTHONPATH=/home/mocuser/moc-public/python-mocutils
cd /home/mocuser/moc-public/python-mocutils/scripts 
./sply.py provision.db
```

### Possible Commands
```
help
#shows all commands


show all
#show everything

show nodes
#shows all active nodes

show free nodes
#shows all free nodes

#destroy vlan [name] 
##delete group under vlan tag

#destroy old switch config for demo
destroy group demo
destroy vlan 108

#create vlan for demo
create vlan 108 

#create group and head node for group
create group demo vlan 108 head-node vlan-head-108

#reshow the newly created group
show group demo

#add nodes to group
add 8,10,12 to demo

#deploy our new virtual machines
deploy

#errors will be seen as you recreate bridges that already exist

#head nodes ip: 192.168.3.28
ssh mocuser@192.168.3.28

#To power-cycle and deploy OpenStack from within the head node:
sudo ./openstack-kickoff

# To power cycle nodes additionally if needed:
#power_cycle 10.0.0.8
#power_cycle 10.0.0.10
#power_cycle 10.0.0.12

#the links to pxelinux.cfg files have now been made for each mac address 
#under /var/lib/tftboot/pxelinux.cfg

# Now a web browser on Death Star's 3-net pointed at 192.168.3.8 will show us a Horizon window.
# Grab a disk image such as the qcow image from https://launchpad.net/cirros/+download

# Something may be wrong with the puppet config for yum installing nova network on the controller.  
yum install openstack-nova-network ; service openstack-nova-network start
# Everything else has the RPM already but will need to be rebooted or have the network restarted
# now that the controller is happily running nova network:
service openstack-nova-network restart
```

