# Sahara Plugins
As you continue to test things out, put information you learn here. Like default passwords, etc. and other things that aren't immediately intuitive to even the advanced Hadoop user.  

Validation means the minimal configuration under which Sahara will launch a cluster. There are other considerations for node processes other than what is listed here. Also note that Sahara will again validate cluster during scaling.

### Vanilla  

**Description**

The Apache Vanilla plugin provides the ability to launch upstream Vanilla Apache Hadoop cluster.

**Version** [2.7.1](http://hadoop.apache.org/releases.html)

**Node processes**

* namenode
* datanode
* secondarynamenode
* resourcemanager
* nodemanager
* historyserver
* oozie
* hiveserver 

**Validation**  

* Cluster must contain exactly one namenode  
* Cluster can contain at most one resourcemanager  
* Cluster can contain at most one secondary namenode  
* Cluster can contain at most one historyserver  
* Cluster can contain at most one oozie and this process is also required for EDP  
* Cluster can’t contain oozie without resourcemanager and without historyserver  
* Cluster can’t have nodemanager nodes if it doesn’t have resourcemanager  
* Cluster can have at most one hiveserver node.  

### Hortonworks ("Ambari plugin")  

**Description**

The Ambari plugin provides a way to provision clusters with Hortonworks Data Platform using Ambari as the orchestrator (blueprints). 

**Version** [2.2](http://hortonworks.com/blog/available-now-hdp-2-2/) on Liberty, 2.3 on Mitaka 

**Node processes**

* Flume
* DataNode
* NameNode
* SecondaryNameNode
* Sqoop
* Ambari
* Ranger Admin
* Ranger Usersync
* ZooKeeper
* Hive Metastore
* HiveServer
* Kafka Broker
* Slider
* YARN Timeline Server
* MapReduce History Server
* NodeManager
* ResourceManager
* DRPC Server
* Nimbus
* Storm UI Server
* Storm Supervisor
* Oozie, Falcon Server
* HBase Master
* HBase RegionServer
* Knox Gateway
* Spark History Server

**Validation**  

* Ensure the existence of Ambari Server process in the cluster  
* Ensure the existence of a NameNode, Zookeeper, ResourceManager(s), HistoryServer and App TimeLine Server in the cluster  

### MapR  

**Description**

The MapR Distribution provides a full Hadoop stack that includes the MapR File System (MapR-FS), MapReduce, a complete Hadoop ecosystem, and the MapR Control System user interface  

**Version** [5.0.0](http://doc.mapr.com/display/RelNotes/Version+5.0+Release+Notes) on Liberty, 5.1.0 on Mitaka  

**Node processes**

* Flume
* Hue
* Hue Livy
* ZooKeeper
* Webserver
* Metrics
* HTTPFS
* HiveMetastore
* HiveServer2
* Pig
* Mahout
* ResourceManager
* NodeManager
* HistoryServer
* Drill
* Oozie
* HBase-Master
* HBase-RegionServer
* HBase-Thrift
* CLDB
* FileServer
* NFS
* Impala-Catalog
* Impala-Server
* Impala-StatestoreSqoop2-Client
* Sqoop2-Server  

**Validation** 

[in-depth](http://docs.openstack.org/developer/sahara/userdoc/mapr_plugin.html#cluster-validation), follow validation rules for MapReduce v2 when applicable 

### Cloudera  

**Description**

The Cloudera plugin provides the ability to launch the Cloudera distribution of Apache Hadoop (CDH) with Cloudera Manager management console  

**Version** [5.4.0](http://blog.cloudera.com/blog/2015/04/cloudera-enterprise-5-4-is-released/) on Liberty, 5.5.0 on Mitaka  

**Node processes**

* IMPALA_CATALOGSERVER
* ZOOKEEPER_SERVER
* IMPALA_STATESTORE
* KMS, FLUME_AGENT
* HUE_SERVER
* CLOUDERA_MANAGER
* YARN_JOBHISTORY
* IMPALAD
* HBASE_REGIONSERVER
* HBASE_MASTER
* OOZIE_SERVER
* HDFS_DATANODE
* SENTRY_SERVER
* YARN_RESOURCEMANAGER
* HDFS_SECONDARYNAMENODE
* HIVE_METASTORE
* HDFS_NAMENODE
* YARN_STANDBYRM
* SOLR_SERVER
* YARN_NODEMANAGER
* KEY_VALUE_STORE_INDEXER
* HDFS_JOURNALNODE
* SPARK_YARN_HISTORY_SERVER
* HIVE_SERVER2
* SQOOP_SERVER
* HIVE_WEBHCAT    

**Validation** [in-depth](http://docs.openstack.org/developer/sahara/userdoc/cdh_plugin.html#cluster-validation)  

### Spark  

**Description**

This plugin provides an ability to launch a standalone Spark installation on a Cloudera HDFS/Hadoop cluster (no management consoles).  

**Version** [1.3.1](https://spark.apache.org/releases/spark-release-1-3-1.html) on Liberty, 1.6.0 on Mitaka  

**Node processes/validation** namenode == 1, master == 1, datanode >= 1, slave >= 1

