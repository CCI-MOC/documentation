These are the changes needed to create 2 node cluster - redundant database and openstack controller
---
**Hiera file parameters needed:**
```
moc::enablefirewall: 'true'
```
**Cluster deployment on/off switch**
```
moc::clusterdeployment: 'true'
```
**Galera settings**
```
galera::server::wsrep_cluster_name: 'clname'
galera::server::wsrep_node_address: "%{ipaddress_eth1}"
galera::server::wsrep_cluster_address: 'gcomm://ip1,ip2'
```
**Pacemaker/corosync settings**
```
ha::vip: xxx.xxx.xxx.xxx
ha::internalvip: xxx.xxx.xxx.xxx
ha::pass: 'somepassword'
ha::node1: 'node1name'
ha::node2: 'node2name'
ha::pubipnode1: 'xxx.xxx.xxx.xxx'
ha::pubipnode2: 'xxx.xxx.xxx.xxx'
```
#Running cluster (re)configuration will cause service interruption - keep below set to !true except when making pcs/cororosync changes
```
ha::runclusterconfig: 'true'
```
#Procedure to build 2 node cluster:
1. Make sure in yaml file: 
	ha::runclusterconfig: 'true'
	On node 1 run
	puppet agent -t --debug
	Very first run fails with 
	Error: Could not retrieve plugin: execution expired
2. Run again
	puppet agent -t --debug
	Next run should finish with
	Debug: Executing '/etc/scripts/clsetup'
	Notice: Caught TERM; calling stop
	This is cluster setup scrip stopping puppet because the second node is needed at this point
**	Run crm_mon on node 1 and continue on node 2**
3. puppet agent -t --debug
	Very first run fails with 
	Error: Could not retrieve plugin: execution expired
	Run again
	puppet agent -t --debug
	When that finishes check crm_mon output on node1 it should be
```
	2 nodes and 3 resources configured

	Online: [ e1-control-01 e1-control-02 ]

	virtual_ip      (ocf::heartbeat:IPaddr2):       Started e1-control-01
	webserver       (ocf::heartbeat:apache):        Started e1-control-01
	intvirt_ip      (ocf::heartbeat:IPaddr2):       Started e1-control-01
```
If not run /etc/scripts/clsetup and perform extra troubleshooting until both nodes and resources are up. 
**After both nodes and all resources are up change ha::runclusterconfig: '!true' in hiera file, continue on node which has the virtual ip resource**

4.Run puppet agent -t --debug, this run will not have mysql errors, galera bootstrap script will detect that and stop puppet with
```
Notice: /Stage[main]/Galera::Server/Exec[bootstrap_galera]/returns: executed successfully
Debug: /Stage[main]/Galera::Server/Exec[bootstrap_galera]: The container Class[Galera::Server] will propagate my refresh event
Debug: Executing '/usr/bin/systemctl is-active auditd'
Debug: Executing '/usr/bin/systemctl is-enabled auditd'
Debug: Executing '/usr/bin/rpm -q openstack-selinux --nosignature --nodigest --qf %{NAME} %|EPOCH?{%{EPOCH}}:{0}| %{VERSION} %{RELEASE} %{ARCH}\n'
Notice: Caught TERM; calling stop
```

Now verify you can connect with mysql and if so run again puppet agent -t --debug - that should complete without errors and there should be fully operational conptroller

5.On the other node run puppet agent -t --debug. This will have mysql errors until galera setup completes, watch for galera setup and after puppet completes verify galera cluster has 2 nodes:
Connect with mysql and run 
```
show status like 'wsrep_%';
```
**wsrep_cluster_size should be 2, if not troubleshoot galera**

6.Run puppet agent -t --debug there should be no errors and 2 node cluster should be complete

Quick guide to updating a 2-node cluster
----------------------------------------

There is always an "active" controller.

Run `crm_mon` to figure out which one is active.

Run `service corosync restart` on the active node to switch to
the other controller.

Only run `puppet agent -t` on the inactive controller.

If a controller is inactive and can't be switched to, and you
don't know why, try running `service corosync restart` on it.

While updating the controllers, always check that the Openstack
services are running correctly on the active node before running
puppet on the inactive node.  (e.g. with 'nova list'.)

From a working starting state, it takes 2 total puppet runs.

You may need to do 3 total puppet runs for 2 controllers, if the
starting state is somewhat broken.  The first run will error out
partway through, the last two will work.

If all the nodes are down at once, then mysql will go down.  To
restart the Galera cluster, run ``mysqld --wsrep-new-cluster`` on
the node that was most recently active.  Then run ``service mysql
start`` on the other node.  Then kill the manually started
``mysqld`` invocation, and run ``service mysql start`` on the
first node.
