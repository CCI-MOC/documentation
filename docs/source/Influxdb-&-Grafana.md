# InfluxDB and Grafana
To visualize the data --such as memory metrics, load metrics and disk metrics-- retrieved from sensu, we use 'InfluxDB v0.8' as the backend database and grafana as the visualization tool. 

We installed influxdb and grafana in moc-sensu server `10.31.27.157`. 

This diagram shows how Sensu,InfluxDB and Grafana interact with each other. 

![](_static/img/influxdb.png)

******

**Influxdb** is a time series, metrics, and analytics database. It’s written in Go and has no external dependencies. 

### Influxdb Configuration

**Ports**

InfluxDB uses port 8083 as the admin server port and 8086 as the http api port by default. More configuration options can be viewed and modified at `/opt/influxdb/shared/config.toml`.

**Configuration related to sensu**

All configuration files related to sensu are inside `/etc/sensu/conf.d`.

This is a .json file configuring host, port, username, password and database for influxdb. 
```
{
  "influxdb": {
    "host": "localhost",
    "port": 8086,
    "username": "root",
    "password": "root",
    "database": "sensu_db"
  }
}
```

**Handler configuration**

This is a handler configuration file we use to convert data directly retrieved from sensu checks to a format that can be send to the database. The configuration .json file can be viewed at `/etc/sensu/conf.d/handler-influxdb-metrics.json`. 
```
{
  "handlers": {
       "influxdb": {
           "type"        : "pipe",
           "command"     : "/etc/sensu/handlers/influxdb-metrics.rb"
     }
  }
}
```

### Logs
You can view influxdb log in the following txt file `/opt/influxdb/shared/log.txt`

### InfluxDB Clustering

**Install  stable versions of InfluxDB**

The stable version of InfluxDB for now is 0.10.1. Install InfluxDB on three separate VMs. And do not start any of the daemons before configuration

**Cluster Node configuration**
```
# General node configuration:
[meta]
...
bind-address="<IP address>:8088"
http-bind-address="<IP address>:8091"

[http]
...
bind-address="<hostname>:8086"
```
*Note: Cannot replace <hostname>  with <IP address> in influxdb versioin 0.10.1. If we set up bind-address with IP address, it won't connect influxdb server.*

**Node Types and Service Types**

* There are three types of nodes: **consensus node**, **hybrid node** and **data node**.
* Service types: **consensus service** and **data service**. 
* There should be an odd number of nodes, at least three, running the consensus service.
* Consensus node and hybrid node can run consensus service.
* There should be at least one node running data service.
* Data node and hybrid node can run data service. 
* I used three hybrid nodes for the first set up. 

### Starting InfluxDB service

**Start the first node**

On one of the VMs, run command, `sudo service influxdb start`

**Point the other two nodes to the first node**

In `/etc/default/influxdb`, add 
```
INFLUXD_OPTS="-join <IP1>:8091"
This <IP1> is the IP address for the first node
```

**Start the other two nodes**

On each of the two VMs, run command `sudo service influxdb start`

### Useful links
* https://docs.influxdata.com/influxdb/v0.10/clustering/cluster_setup/

******
### Grafana Installation
The Grafana version we are using is v 2.0.0-beta3

You can install Grafana using yum directly.
```
$ sudo yum install https://grafanarel.s3.amazonaws.com/builds/grafana-2.0.0_beta3-1.x86_64.rpm
```
Or you can install it using rpm.
```
$ sudo yum install initscripts fontconfig
$ sudo rpm -Uvh grafana-2.0.1-1.x86_64.rpm
```

### Configuration

**Port**

The default port of grafana is 3000. As it is the same as port of uchiwa — sensu’s dashboard, we change the port to 3031 in the configuration file located at `/etc/grafana/grafana.ini`.

### Start the Server
You can start the server using: `$ sudo service grafana-server start`

If you would like to configure to start grafana at boot time, just do `$ sudo /sbin/chkconfig --add grafana-server`

You can also start the server via systemd,
```
$ systemctl daemon-reload
$ systemctl start grafana-server
$ systemctl status grafana-server
```

### Logs
Log files are located at `/var/log/grafana`

