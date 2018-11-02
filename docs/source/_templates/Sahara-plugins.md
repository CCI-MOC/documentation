As you continue to test things out, put information you learn here. Like default passwords, etc. and other things that aren't immediately intuitive to even the advanced Hadoop user.  

Validation means the minimal configuration under which Sahara will launch a cluster. There are other considerations for node processes other than what is listed here. Also note that Sahara will again validate cluster during scaling.

## Vanilla  
* _Description_: The Apache Vanilla plugin provides the ability to launch upstream Vanilla Apache Hadoop cluster.
* _Version_: [2.7.1](http://hadoop.apache.org/releases.html)
* _Node processes_: namenode, datanode, secondarynamenode, resourcemanager, nodemanager, historyserver, oozie, hiveserver 
* _Validation_:  
    * Cluster must contain exactly one namenode  
    * Cluster can contain at most one resourcemanager  
    * Cluster can contain at most one secondary namenode  
    * Cluster can contain at most one historyserver  
    * Cluster can contain at most one oozie and this process is also required for EDP  
    * Cluster can’t contain oozie without resourcemanager and without historyserver  
    * Cluster can’t have nodemanager nodes if it doesn’t have resourcemanager  
    * Cluster can have at most one hiveserver node.  

## Hortonworks ("Ambari plugin")  
* _Description_:  The Ambari plugin provides a way to provision clusters with Hortonworks Data Platform using Ambari as the orchestrator (blueprints). 
* _Version_: [2.2](http://hortonworks.com/blog/available-now-hdp-2-2/) on Liberty, 2.3 on Mitaka 
* _Node processes_: Flume, DataNode, NameNode, SecondaryNameNode, Sqoop, Ambari, Ranger Admin, Ranger Usersync, ZooKeeper, Hive Metastore, HiveServer, Kafka Broker, Slider, YARN Timeline Server, MapReduce History Server, NodeManager, ResourceManager, DRPC Server, Nimbus, Storm UI Server, Storm Supervisor, Oozie, Falcon Server, HBase Master, HBase RegionServer, Knox Gateway, Spark History Server
* _Validation_:  
    * Ensure the existence of Ambari Server process in the cluster  
    * Ensure the existence of a NameNode, Zookeeper, ResourceManager(s), HistoryServer and App TimeLine Server in the cluster  

## MapR  
* _Description_: The MapR Distribution provides a full Hadoop stack that includes the MapR File System (MapR-FS), MapReduce, a complete Hadoop ecosystem, and the MapR Control System user interface  
* _Version_: [5.0.0](http://doc.mapr.com/display/RelNotes/Version+5.0+Release+Notes) on Liberty, 5.1.0 on Mitaka  
* _Node processes_:  Flume, Hue, Hue Livy, ZooKeeper, Webserver, Metrics, HTTPFS, HiveMetastore, HiveServer2, Pig, Mahout, ResourceManager, NodeManager, HistoryServer, Drill, Oozie, HBase-Master, HBase-RegionServer, HBase-Thrift, CLDB, FileServer, NFS, Impala-Catalog, Impala-Server, Impala-StatestoreSqoop2-Client, Sqoop2-Server  
* _Validation_: [in-depth](http://docs.openstack.org/developer/sahara/userdoc/mapr_plugin.html#cluster-validation), follow validation rules for MapReduce v2 when applicable 

## Cloudera  
* _Description_: The Cloudera plugin provides the ability to launch the Cloudera distribution of Apache Hadoop (CDH) with Cloudera Manager management console  
* _Version_: [5.4.0](http://blog.cloudera.com/blog/2015/04/cloudera-enterprise-5-4-is-released/) on Liberty, 5.5.0 on Mitaka  
* _Node processes_: IMPALA_CATALOGSERVER, ZOOKEEPER_SERVER, IMPALA_STATESTORE, KMS, FLUME_AGENT, HUE_SERVER, CLOUDERA_MANAGER, YARN_JOBHISTORY, IMPALAD, HBASE_REGIONSERVER, HBASE_MASTER, OOZIE_SERVER, HDFS_DATANODE, SENTRY_SERVER, YARN_RESOURCEMANAGER, HDFS_SECONDARYNAMENODE, HIVE_METASTORE, HDFS_NAMENODE, YARN_STANDBYRM, SOLR_SERVER, YARN_NODEMANAGER, KEY_VALUE_STORE_INDEXER, HDFS_JOURNALNODE, SPARK_YARN_HISTORY_SERVER, HIVE_SERVER2, SQOOP_SERVER, HIVE_WEBHCAT    
* _Validation_: [in-depth](http://docs.openstack.org/developer/sahara/userdoc/cdh_plugin.html#cluster-validation)  

## Spark  
* _Description_: This plugin provides an ability to launch a standalone Spark installation on a Cloudera HDFS/Hadoop cluster (no management consoles).  
* _Version_: [1.3.1](https://spark.apache.org/releases/spark-release-1-3-1.html) on Liberty, 1.6.0 on Mitaka  
* _Node processes/validation_: namenode == 1, master == 1, datanode >= 1, slave >= 1