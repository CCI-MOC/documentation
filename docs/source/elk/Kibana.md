## Kibana
Kibana is an open source analytics and visualization platform designed to work with Elasticsearch.

You use Kibana to search, view, and interact with data stored in Elasticsearch indices. 
You can easily perform advanced data analysis and visualize your data in a variety of charts, tables, and maps.

### Installation 
Use the following commands to download and run Kibana.
```shell
curl -L -O https://download.elastic.co/kibana/kibana/kibana-4.4.0-linux-x64.tar.gz
tar xzvf kibana-4.4.0-linux-x64.tar.gz
cd kibana-4.4.0-linux-x64/
./bin/kibana
```

Don't run Kibana yet because we still need to configure it.

### Configuration
In the `kibana-4.4.0-linux-x64` directory edit the `config/kibana.yml` file so it looks like the following.
```shell
# Kibana is served by a back end server. This controls which port to use.
server.port: 5601

# The host to bind the server to.
server.host: "192_IP_ADDRESS"

# If you are running kibana behind a proxy, and want to mount it at a path,
# specify that path here. The basePath can't end in a slash.
# server.basePath: ""

# The Elasticsearch instance to use for all your queries.
elasticsearch.url: "http://ES_host_ip:9200"

# preserve_elasticsearch_host true will send the hostname specified in 'elasticsearch'. If you set it to false,
# then the host you use to connect to *this* Kibana instance will be sent.
# elasticsearch.preserveHost: true

# Kibana uses an index in Elasticsearch to store saved searches, visualizations
# and dashboards. It will create a new index if it doesn't already exist.
kibana.index: ".kibana"

# The default application to load.
kibana.defaultAppId: "discover"

# Time in milliseconds to wait for elasticsearch to respond to pings, defaults to
# request_timeout setting
elasticsearch.pingTimeout: 1500

# Time in milliseconds to wait for responses from the back end or elasticsearch.
# This must be > 0
elasticsearch.requestTimeout: 300000

# Time in milliseconds for Elasticsearch to wait for responses from shards.
# Set to 0 to disable.
elasticsearch.shardTimeout: 0
```

Now start and enable Kibana service
```shell
sudo systemctl start kibana
sudo systemctl enable kibana
```

Now that Kibana is configured go to the `192_IP_ADDRESS:5601` to view it.

### Start using Kibana
Before you can start using Kibana, you need to tell it which Elasticsearch indices you want to explore.

The first time you access Kibana, you are prompted to define an index pattern that matches the name of one 
or more of your indices.You can add index patterns at any time from the Settings tab.
 1. Point your browser at port 5601 to access the Kibana UI. 
 For example, `localhost:5601` or `http://YOURDOMAIN.com:5601`
 1. Specify an index pattern that matches the name of one or more of your Elasticsearch indices. 
 In our production the index name is `filebeat-*`. asterisk (`*`) matches zero or more characters in an index’s name. 
 If your Elasticsearch indices follow some other naming convention, enter an appropriate pattern. 
 The "pattern" can also simply be the name of a single index.
 1. Select the index field that contains the timestamp that you want to use to perform time-based comparisons. 
 Kibana reads the index mapping to list all of the fields that contain a timestamp.
 If your index doesn’t have time-based data, disable the **Index contains time-based events** option.
 1. If new indices are generated periodically and have a timestamp appended to the name, select the 
 **Use event times to create index names** option and select the **Index pattern interval**.
 This improves search performance by enabling Kibana to search only those indices that could contain data 
 in the time range you specify. This is primarily applicable if you are using Logstash to feed data into Elasticsearch.
 1. Click **Create** to add the index pattern. This first pattern is automatically configured as the default. 
 When you have more than one index pattern, you can designate which one to use as the default **from Settings > Indices**.

For more information, check the documentation [here](https://www.elastic.co/guide/en/kibana/current/index.html)

### Data Discovery and Visualization
 -  Search and browse your data interactively from the **Discover** page.
 -  Chart and map your data from the **Visualize** page.
 -  Create and view custom dashboards from the **Dashboard** page

From Kibana’s Discover page, we can submit search queries, filter the results, and examine the data 
in the returned documents.

You can construct visualizations of your search results from the Visualization page. 
Each visualization is associated with a search.

You can save and share visualizations and combine them into dashboards to make it easy to correlate related information. 
