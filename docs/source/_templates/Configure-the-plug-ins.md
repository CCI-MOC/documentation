## Introduction

Ways to build clusters from UI, including how to run examples for Pig & Spark
http://docs.openstack.org/developer/sahara/horizon/dashboard.user.guide.html#data-sources

Two data source for Sahara
1) Swift object, can be added from GUI swift://container_name/file_name
2) HDFS destination, can be anywhere by hdfs://ip:port/url.

### Vanilla

A Video can be found here 
https://www.mirantis.com/blog/sahara-updates-in-openstack-juno-release/

### HDP **(NOT SUCC YET)**
http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/bk_cluster-planning-guide/content/ch02s02.html

### Cloudera
http://docs.openstack.org/developer/sahara/userdoc/cdh_plugin.html

### Apache Spark

(*) Note that Spark uses the Hadoop-client library to talk to HDFS. Because the HDFS protocol has changed in different versions of Hadoop, you must build Spark against the same version that your cluster uses. By default, Spark links to Hadoop 1.0.4. You can change this by setting the SPARK_HADOOP_VERSION variable when compiling. A list of supported Hadoop distributions is available here 
http://docs.openstack.org/developer/sahara/userdoc/spark_plugin.html

