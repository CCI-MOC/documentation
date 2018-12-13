# Sahara End-User Guide
This guide explains the use of OpenStack Sahara as configured in the MOC. Sahara provides virtualized Big Data as a Service. 

The following will be a walkthrough of both the cluster and job operations that Sahara provides, with plenty of helpful tips (and warnings) sprinkled throughout. If you have questions, or discover bugs, email MOC at team@lists.massopen.cloud.  

**Accessing the Sahara UI** 

If your OpenStack user has been given the sahara_user role, you will see the tab "Data Processing" in the "Project" section of Horizon.

### Cluster creation

**Plugin selection**

Initally, three plugins will be offered. Each is considered "simple", as there are no management consoles like in Cloudera, Hortonworks, etc. (These distributions will be supported in MOC Sahara eventually.)
* **Vanilla** : Offers upstream Hadoop 2.7.1 and Hive 0.11.0, plus Oozie and YARN for job management
* **Spark** : Offers Spark 1.3.1 on a Cloudera fork of HDFS
* **Storm** : Offers Storm 0.9.2

**Node group template design**

Navigate to Data Processing → Node Group Templates → Create Template
* **Name** : Must follow rules for valid hostname  
* **Flavor** : Make sure to move away from default m1.nano, since this will probably cause kernel panic and crash Sahara engine  
* **Availability Zone** : Just set to "nova"
* **Storage location** : choice between Ephemeral and Cinder
 * Ephemeral for speed
 * Cinder more reliable for HDFS replicas (warning: make sure to set Availabilty Zone)
* **Node processes**: Keep in mind validation rules  
 * Vanilla: namenode == 1, (resourcemanager, secondary namenode, historyserver, oozie, hiveserver) <= 1; Oozie depends on resourcemanger and historyserver, Nodemanager depends on resourcemanager  
 * Spark: namenode == 1, master == 1, datanode >= 1, worker >= 1  
 * Storm: nimbus == 1, zookeeper == 1   
* **Parameters** : Available settings depend on which processes have been selected; you may wish to set dfs.datanode.du.reserved, Heap Size for various processes
* **Security** : Auto Security Group feature should handle opening the proper ports  

**Cluster template design**

Navigate to Data Processing → Cluster Templates → Create Template
* **Name** : Must follow rules for valid hostname
* **Anti-Affinity** : Makes sure that instances start on different compute hosts. This is good for reliable HDFS replication. You are bound by number of compute hosts (14 in Kaizen, 3 in Engage1)
* **Node Groups** : 
    * Plugin must match
    * Note that if you edit a Node Group Template, you must delete and readd from Cluster Template
* **Parameters** : Available settings depend on which processes have been selected; you may wish to set dfs.replication (defaults to number of data/worker nodes), dfs.blocksize

**Cluster launching and scaling**

Navigate to Data Processing → Cluster Templates → Launch Cluster  
* **Name** : Must follow rules for valid hostname
* **Cluster Template** : Plugin must match
* **Cluster Count** : Create "separate" clusters - if you want more nodes, then you can scale cluster later, or edit cluster template  
* **Base Image** : There is a choice of Ubuntu 14.04 or CentOS 6.6 for Vanilla plugin; Spark and Storm offer Ubuntu 14.04 only  
* **Keypair** : Highly recommended, since images do not have default passwords  
* **Neutron Management Network** : DNS highly recommended; network must have been created redundantly across both OpenStack controllers  
* **Scale Cluster** : Validation rules still apply

**Cluster access**

* **Floating IP** : Not assigned automatically; recommended that you assign to master node  
* **Web UIs**: SOCKS proxy may come in handy; Ubuntu images support X forwarding so it may be useful to install a lightweight browser like Midori on one of your instances  
* **Usernames** : "ubuntu" for Ubuntu images, "cloud-user" for CentOS 6  

### Job execution

**Swift integration**

Jobs on Sahara clusters support Swift I/O. To be more specific, this means you can write a Swift path instead of an HDFS path in any MapReduce or Spark job. (Hive support coming soon, hopefully.) Here are some helpful hints regarding the use of Swift:  
* **Paths** : Format is `swift://\<container\>.sahara/path/to/file`
* **Authentication** : Username and password must be passed at runtime. If you are using the Sahara API/UI to submit jobs, then this may be able to be abstracted away by creating a `Data Source` (More about this in the next section). Username and password are set by `fs.swift.service.sahara.username` and `fs.swift.service.sahara.password`. The value `fs.swift.service.sahara.tenant` is automatically set when the cluster is first created, but you can override it if needed.  
* **Easy transfer** : It may be easier to move data from Swift into HDFS first, before running a job. You can use the "distributed copy" command, e.g. `hadoop distcp -D fs.swift.service.sahara.username=USERNAME -D fs.swift.service.sahara.password=PASSWORD INPUT_PATH OUTPUT_PATH`  

**Job submission through UI**

* **Data Sources** : A useful abstraction of Swift paths (credentials applied automatically, too!) or HDFS paths  
    * **Name** : no spaces  
    * **Data Source Type** : Swift or HDFS, but Sahara doesn't validate that your path or credentials are valid, so be careful  
    * **URL**: Just regular HDFS path, or `swift://\<container\>.sahara/path` . Username and password are required for Swift! (not automatically populated)  
* **Job Binaries** : These are any jar file, script, etc. that you submit with your job. 
    * Advised that you store in Swift, since otherwise it is going in MySQL...  
* **Job Templates** : This is where the actual job is designed
    * **Name** : no spaces  
    * **Main Binary** : for Hive, Shell, Spark, and Storm
    * **Libs** : any additional files, or more commonly the main binary for Java/MapReduce  
    * **Interface Arguments** : Allows you to add custom fields to the job launching form, for args and config parameters (will come in handy in next section)  
* **Jobs** : (launch from Data Processing → Job Templates → Launch on Existing Cluster)
    * **Cluster** : Make sure to choose the right one!  
    * **Input/Output**: These only show up for MapReduce and Hive jobs. Otherwise you will set your input and output paths as args (or they could be hardcoded in your job)  
    * **Main Class** : Required for Java and Spark jobs, equivalent to setting edp.java.main_class in Configuration section (or by interface argument)    
    * **Configuration - Swift** : `edp.substitute_data_source_for_name` set to `True` allows you to use `datasource://\<source_name\>` in args -- otherwise you can pass `fs.swift.service.sahara.username` or `fs.swift.service.sahara.password` and use regular `swift://` style paths  
    * **Configuration - MapReduce** : `mapred.mapper.class` and `mapred.reducer.class` need to get passed in as configuration values, but the UI does not prompt for them  
    * **Arguments** : These are command line arguments, often used for input and output args  
    * **Interface Arguments**: If you designed these in Job Template step, you will see them here. If they are set as required, you will get reminded to set them if you forget, which can be very helpful since there can be many parameters you have to remember to pass for jobs with Swift I/O, etc.  
    * **Status** (after launching): PENDING, RUNNING, etc. are not very descriptive. Recommended to use web console for your cluster. Oozie is good for this on Vanilla cluster. For Spark and Storm, you can also look in `/tmp/\<PLUGIN\>-edp/\<JOB_TEMPLATE\>/\<JOB_ID\>/` , where files like `launch_command.log` and stderr in that folder are very useful. 

### Manual interaction  
* **Where programs live**  
    * **Vanilla** : `/opt/hadoop`, `/opt/hive`  
    * **Spark** : `/opt/spark`  
    * **Storm** : `/usr/local/`
* **Permissions**
    * Spark and Storm just have "ubuntu" user for everything
    * Vanilla: Many Hadoop files and programs belong to "hadoop" user

