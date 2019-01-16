# Sahara Comparisons

### Openstack Sahara vs. Azure HDInsight, Amazon EMR, and Google Dataproc
The following is a comparison of three “managed Hadoop” cloud offerings (Microsoft’s Azure HDInsight, Amazon’s Elastic MapReduce "EMR", and Google’s Dataproc) to each other and to Openstack Sahara.

There are some obvious/basic similarities, like the fact that they are dynamically scaled (change the size of the cluster at any time) Hadoop-as-a-Service. However, each platform has its nuances:

### Microsoft Azure HDInsight
HDInsight is notable in that it runs a distribution of Hadoop from Hortonworks, rather than the “pure” Apache version. Upon configuration, there is a choice between Hadoop, HBase, Storm, Spark, or R Server– running on Ubuntu by default, but interestingly enough with the option of Windows Server. 

The addition of Windows Server is a nice bonus for those who want to write MapReduce programs in .NET rather than in Java. 

You then choose your data source, number of nodes, and hardware specifications. In my own experience with deploying an HDInsight cluster, it is the slowest choice– allow 15 minutes to get up and running. 

Once deployed, SSH and Ambari (via the web interface or REST API)  are already preconfigured and started, allowing you to run Hadoop tasks. Input and output data from Hadoop is all stored in Azure blobs, which allow for faster write times than regular HDFS. Excel 2010 and newer can also push/pull data direct from HDInsight.

### Amazon EMR
EMR runs the standard Apache distribution of Hadoop. There is a choice between Core Hadoop, HBase, Presto-Sandbox, and Spark during setup and then the usual process of choosing your data source, number of nodes, and hardware specifications. 

Deployment is fast, taking no more than 10 minutes (and probably less). You can then control your cluster via SSH (your nodes run Debian) or REST API. 

Notably, there are no Hadoop web interfaces open by default; however, there is an option to “add step” from the AWS web console which is an easy way to run Hadoop tasks (MapReduce jars, Hive scripts, etc.). 

Storage is typically in an S3 bucket, but you can also use DynamoDB. 

One unique feature of EMR is the option to use Cloudera’s Impala query language instead of Hive. 

Amazon also claims excellent load balancing ability. (This is related to the "E" for "elastic" in Amazon EMR and EC2.)

### Google Dataproc
Dataproc also runs the standard Apache distribution of Hadoop. The only choices presented during deployment deal with data source, number of nodes, and hardware specifications.

In my experience, Dataproc has the fastest deployments. (Still allow up to 10 minutes.) Nodes can be controlled over the standard SSH or REST API (a Python wrapper for the API allows you to accomplish some tasks). 

There is also a form on the Google Cloud web console called “submit job” to run Hadoop tasks like MapReduce jars, Hive scripts, etc. Data must be stored in Google Cloud Storage.

A very unique and useful feature of Dataproc is that any view or form in web console can be translated to the equivalent REST request or response.

### Shortcomings of Azure, Amazon, and Google offerings
It is worth noting that one feature that none of the three offer is the ability to make “snapshots” of the nodes/VMs.

Rather than configure the software on one node and “freeze” it as a template, one must instead write bash scripts (known as script/bootstrap/installation "actions") to be run upon the deployment of a new node.

### Cost comparison of Azure, Amazon, and Google offerings
[Sample costs](https://cloud.google.com/blog/big-data/2016/03/understanding-cloud-pricing-part-6-big-data-processing-engines)

Tasked with crunching 50TB of data for 5 hours daily (from cheapest to most expensive):
* **Dataproc** : 21-node cluster with 4 virtual cores, 15GB of RAM, and 80GB of SSD disk per node → $2154.40
* **EMR** : 21-node cluster with 4 cores, 15GB RAM, and 2x40 GB SSD storage per node → $2613.27
* **HDInsight** (closest equivalent): 20-node cluster with 4 cores, 14GB RAM, and 200GB disk per node → $3261.33

*Note that Dataproc and HDInsight will in practice bill by the minute. EMR will end up charging a lot more as it rounds up to the whole hour.*

### Comparisons to Openstack Sahara
Openstack Sahara provides easy setup of “elastic Hadoop on demand” (very similar to the three services above) within an OpenStack environment. Clusters and nodes are configured via “templating”, a process unique to Sahara.

The process of templating can broken down into at least three distinct steps:
* **Choosing Hadoop distribution** (vanilla, Cloudera, Hortonworks), or opting for Spark/Storm
* **Preconfiguring parameters** for components such as HDFS, Yarn, Hive, Hue, Oozie — this is the most unique part of the templating process: easier than writing a shell script but with the tradeoff that a shell script can do anything and templating is limited to setting Hadoop-related parameters
* **Hardware specifications** (defined as “flavors”)

Once deployed, all tasks can be run either from the Openstack Horizon web interface via the “create job binary” webform, or (as is typical) SSH or a REST API. Notably, and very usefully, Openstack provides a Python client/wrapper around this API.

Storage is generally in Openstack’s Swift (object storage), but Hadoop jobs can also be kept in Sahara’s “internal database”. 

It is therefore evident that Openstack Sahara is among the best choices, as it allows for the following advantages over the other Hadoop cloud offerings:
* The most options for Hadoop distributions: “Vanilla” OR Cloudera OR Hortonworks
* Simplest way to preconfigure nodes/clusters: templating instead of the messy script/bootstrap/installation actions
* Full-fledged Python client/wrapper for API (matched only by Google’s)

One potential downside to Openstack Sahara is that one must manually assign the required processes to allow for Elastic Data Processsing. Whereas the other services automatically provision nodes so that all the required processes are running, Sahara requires manual assignment of the following processes: (in the case of Vanilla Hadoop 2.x)
* namenode
* datanode
* resourcemanager
* nodemanager
* historyserver
* oozie

*Note that many processes can be combined onto one node.

