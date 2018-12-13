# Elastic Search
Elasticsearch is a popular open source search server that is used for real-time distributed search and analysis of data. The operating system used in this tutorial is CentOS 7. 

This tutorial is for single node elasticsearch deployment (cluster size is 1).

### Install Elasticsearch
Elasticsearch requires at least Java 8.

```
cd ~
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u73-b02/jdk-8u73-linux-x64.rpm"
sudo yum -y localinstall jdk-8u73-linux-x64.rpm
```

Now Java should be installed at `/usr/java/jdk1.8.0_73/jre/bin/java`, and linked from `/usr/bin/java`.

You may delete the archive file that you downloaded earlier:

	rm ~/jdk-8u73-linux-x64.rpm

Run the following command to import the Elasticsearch public GPG key into rpm:

	sudo rpm --import http://packages.elastic.co/GPG-KEY-elasticsearch

Create a new yum repository file for Elasticsearch.

```
echo '[elasticsearch-2.x]
name=Elasticsearch repository for 2.x packages
baseurl=http://packages.elastic.co/elasticsearch/2.x/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
' | sudo tee /etc/yum.repos.d/elasticsearch.repo
```

Install Elasticsearch with this command:

	sudo yum -y install elasticsearch

### Configure Elasticsearch
Open the Elasticsearch configuration file for editing:

	sudo vi /etc/elasticsearch/elasticsearch.yml

Though in this tutorial we will only deploy elasticsearch in one node. It is recommended to set a cluster name for future deployment when we want to set a cluster nodes of elasticsearch. You will want to use a descriptive name that is unique (within your network).

Find the line that specifies `cluster.name`, uncomment it, and replace its value with your desired cluster name. In this tutorial, we will name our cluster "moc-elasticsearch"

	cluster.name: moc-elasticsearch

Next, we will set the name of each node. This should be a descriptive name that is unique within the cluster.

Find the line that specifies `node.name`, uncomment it, and replace its value with your desired node name. In this tutorial, we will set node name to the hostname of server by using the `${HOSTNAME}` environment variable:

	node.name: ${HOSTNAME}

Elastic recommends to avoid swapping the Elasticsearch process at all costs, due to its negative effects on performance and stability. One way avoid excessive swapping is to configure Elasticsearch to lock the memory that it needs.

Find the line that specifies `bootstrap.mlockall` and uncomment it:

	bootstrap.mlockall: true

Save and exit.

Next, open the `/etc/sysconfig/elasticsearch` file for editing

	sudo vi /etc/sysconfig/elasticsearch

First, find `ES_HEAP_SIZE`, uncomment it, and set it to about 50% of your available memory. For example, if you have about 8 GB free, you should set this to 4 GB (4g):

	ES_HEAP_SIZE=4g

Next, find and uncomment `MAX_LOCKED_MEMORY=unlimited`. It should look like this when you're done:

	MAX_LOCKED_MEMORY=unlimited

Save and exit.

The last file to edit is the Elasticsearch systemd unit file. Open it up for editing:

	sudo vi /usr/lib/systemd/system/elasticsearch.service

Find and uncomment `LimitMEMLOCK=infinity`. It should look like this when you're done:

	LimitMEMLOCK=infinity

Save and exit.

Now reload the systemctl daemon and start Elasticsearch:

```
sudo systemctl daemon-reload
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch
```

### Verify installation
If everything was configured correctly, your Elasticsearch cluster should be up and running. You could use this command to check the health of your cluster (which has one node currently):

	curl -XGET http://localhost:9200/_cluster/health?pretty

Elasticsearch is running in good condition if you get response that looks like this:
```
{
  "cluster_name" : "elasticsearch",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 633,
  "active_shards" : 633,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
```

### Indexing
While we may want to use ElasticSearch primarily for searching the first step is to populate an index with some data, meaning the "Create" of CRUD, or rather, "indexing".

In ElasticSearch indexing corresponds to both "Create" and "Update" in CRUD - if we index a document with a given type and ID that doesn't already exists it's inserted. If a document with the same type and ID already exists it's overwritten.

**Document**

JSON document stored in ES. Like row in table in Relational DB. Documents are collections of fields, and comprise the base unit of storage in elasticsearch.

**Index** 

The largest single unit of data in elasticsearch is an index. Like Database in relational db, logical namespace which maps to primary and replica shard.

**Id**

Uniquely identifies a document.

**Field**

Key-value pairs. Like column in Relational DB 
* Simple value like string, integer, date
* Array or an object

**Type** 

Like a table in relational DB. Has a lot of fields

**Document**

oriented - Stores entire objects or documents (also indexes the contents of each document in order to make them searchable)

## ELK Stack Configuration
* In Elasticsearch configuration `elasticsearch.yml` should listen on an address `network.host: ES_private_IP` that is accessible to Logstash and Kibana.
* In Kibana configuration `kibana.yml` `elasticsearch_url` should be set to Elasticsearch's listening IP/port `elasticsearch_url: "http://ES_private_IP:9200"`.
* Logstash needs its output configured to point to the Elasticsearch server.

### Elasticsearch requests

```
# curl -X<VERB> '<PROTOCOL>://<HOST>/<PATH>' -d '<BODY>'
# curl -X<VERB> '<PROTOCOL>://<HOST>/<PATH>?<QUERY_STRING>'
```

In the example below, the PUT request is used to index a first JSON object to the REST API to a URL made up of the index name, type name and ID. ID is optional (if you don't specify an ID, elasticsearch will provide one).

```
# curl -XPUT 'http://localhost:9200/index_1/type_1/id_1' -d'
{
	"email": "john@smith.com",
	"first_name": "John",
	"last_name": "Smith",
	"info":{
	    "age": 25,
	    "phone_number": 123
	    }
}
```

### Sense 
Interactive console (chrome plugin) tool to query elasticsearch. Can be downloaded from [Sense](https://chrome.google.com/webstore/detail/sense-beta/lhjgkmllcaadmopgmanpapmpjgmfcfig?hl=en). Easy to use with shortcuts and organizes output into human-readable format. 

example shortcuts

	<VERB> /<PATH> '<body>'

instead of 

	curl -X<VERB> '<PROTOCOL>://<HOST>/<PATH>' -d '<BODY>'

![](http://cdn.shahed.me/es-fulltext.png)


### Simple CRUD example
```
// Create a type called 'hacker'(the index planet must be created first)
# curl -XPUT "https://localhost:9200/planet/hacker/_mapping" -d'
{
  "hacker": {
    "properties": {
      "handle": {"type": "string"},
      "age": {"type": "long"}}}}'

// Create a document
# curl -XPUT "http:localhost:9200/planet/hacker/1" -d'
{"handle": "jean-michel", "age": 18}'

// Retrieve the document
# curl -XGET "http:localhost:9200/planet/hacker/1" 

// Update the document's age field
# curl -XPOST "http:localhost:9200/planet/hacker/1/_update" -d'
{"doc": {"age": 19}}'

// Delete the document
# curl -XDELETE "http:localhost:9200/planet/hacker/1"

// Create an index named 'planet'
# curl -XPUT "http:localhost:9200/planet"
```

