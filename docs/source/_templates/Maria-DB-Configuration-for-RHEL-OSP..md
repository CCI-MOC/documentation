MariaDB configuration:

MariaDB has the advantage of synchronous replication in active-active multimaster configuration. 

Following is the configurational setup that we need to do:

Open file /etc/my.conf.d/galera.conf:

bind-address=129.10.3.47(IPAddress of the host).
wsrep_provider=/usr/lib64/galera/libgalera_smm.so(Galera file)
wsrep_provider_options="socket.ssl_key=/etc/openstack-common/ssl.key/nimbus-0.csail.mit.edu.key;socket.ssl_cert=/etc/openstack-common/ssl.crt/nimbus-0.csail.mit.edu.crt;socket.ssl_ca=/etc/openstack-common/ssl.crt/incommon-sha384-combined.crt;socket.ssl_cipher=AES128-SHA
(SSL options)
wsrep_cluster_name="os_galera_test_cluster"(Cluster name)
wsrep_cluster_address="gcomm://128.52.128.15,128.52.129.116,129.10.3.32"(The ipaddresses of all nodes in the cluster).
wsrep_sst_auth='root:<pwd>'(Authentication for remote host).
wsrep_sst_method=rsync(Method for syncing with nodes).


Methods for starting/stopping services:
sudo -u mysql /usr/libexec/mysqld --wsrep-cluster-address='gcomm://128.52.129.116,129.10.3.32,128.52.129.15'


Reference:
http://docs.openstack.org/high-availability-guide/content/ha-aa-db-mariadb-galera-rh.html