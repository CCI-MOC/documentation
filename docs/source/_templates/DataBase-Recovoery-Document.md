This needs organizing!

When joining a new node
------------------------

Make sure it syncs from a secondary node not one of the active masters as the 'donor' node may be unresponsive to normal requests during the sync (depending on sync method) and even if it is technically responsive the sync operation is very resource intensive so it will definitely be less responsive.

http://galeracluster.com/documentation-webpages/mysqlwsrepoptions.html#wsrep-sst-donor

The donor can be specified using the `--wsrep-sst-donor` commandline flag.

Note this expect the 'wsrep_node_name' of the donor not it's ip address or DNS name.  To verify this value you can run the following on the desired **donor** node.

    mysql -e "show variables like 'wsrep_node_name';"

This is usually the unqualified hostname, but best to double check.

If the **Whole** cluster goes down
-----------------------------------

http://galeracluster.com/documentation-webpages/restartingcluster.html


**Never stop all nodes in a cluster**, but if you do start the "most
advanced one first"

/var/lib/mysql/grastate.dat holds saved replication state

    # GALERA saved state
    version: 2.1
    uuid:    5ee99582-bb8d-11e2-b8e3-23de375c1d30
    seqno:   8204503945773
    cert_index:


https://mariadb.com/kb/en/mariadb/getting-started-with-mariadb-galera-cluster/#restarting-the-cluster

If you shut down all nodes, you effectively terminated the cluster
(not the data of course, but the running cluster), hence the right way
is to start the all the nodes with gcomm://<node1 address>,<node2
address>,...?pc.wait_prim=no again. On one of the nodes set global
wsrep_provider_options="pc.bootstrap=true";

the "most advanced node" should get the pc.bootstrap=true treatment.    

    seqno:   -1

means the node crashed and is likely inconsistent, will take a full
copy to resync when it joins

If *all* nodes crashed (unlikely in a multi site setup) you have no way to tell which was "most advanced" in our
world with a single specific active node it's going to be the one that
was active when the world ended (ie that active openstack controller)

That will need a manual mysql start with '--wsrep-new-cluster' flag

Joining the second crashed node will be faster if you temporarily set:
    [mysqld]
    wsrep_sst_method = rsync

(rather than mysqldump)

Once this is complete, on second node:

    MariaDB [(none)]> SHOW GLOBAL STATUS LIKE 'wsrep_local_state_comment';
    +---------------------------+--------+
    | Variable_name             | Value  |
    +---------------------------+--------+
    | wsrep_local_state_comment | Synced |
    +---------------------------+--------+

You can then shutdown the manually started mysqld on the 1st node by
running 'mysqladmin shutdown' then restart normally 'service mysql
start'

Clients can now start running against the primary node.

The second node can then be used as the donor for syncing other
crashed nodes leaving the 'active' master free to handle real
requests.


Galera cluster starting(Kaizen steps):

The way to bring up databases in galera cluster:

-Never bring down all nodes in the cluster in the same time(Galera is multimaster).

-If at all you bring down. First start the node with latest changes(active controller in our cases) using this command:

sudo -u mysql /usr/libexec/mysqld --wsrep-cluster-address='gcomm://' &
(This is for bootstrapping this node as primary).

- On the secondary node(passive one) use the following:
sudo -u mysql /usr/libexec/mysqld --wsrep-cluster-address='gcomm://129.10.3.47,129.10.3.32' &

-Check for secondary node(passive controller) synced state:
MariaDB [(none)]> SHOW GLOBAL STATUS LIKE 'wsrep_local_state_comment';
+---------------------------+--------+
| Variable_name | Value |
+---------------------------+--------+
| wsrep_local_state_comment | Synced |
+---------------------------+--------+

-Once you see the see the status as synced restart the services on active node using following commands:
mysqladmin shutdown
service mariadb start

This should bring the services on the DB nodes.

