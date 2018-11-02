Following the wiki Logging IPMI sensor data into Sensu + Influxdb, this is a quick walk-through to find the ipmi checks

To view IPMI sensu checks, ssh into the haas master and then to the sensu-ipmi server
```
$ ssh username@129.10.5.48
$ ssh username@192.168.122.147

```

The checks for IPMI sensors are found at ```/etc/sensu/conf.d/ipmi```. Here is an example:
```
{
  "checks":{
    "ipmi_sensor_metrics_17":{
      "handlers": ["default", "influxdb-ipmi-sensor"],
      "type": "metric",       
      "command": "/etc/sensu/plugins/ipmi-sensor-metrics.rb -u admin -p 73qtx8nVXa4c06 -h 10.99.1.117 --scheme  compute-17.moc.ne.edu",
      "interval": 10,
      "subscribers": [ "moc-ipmi" ]
     }
  }
}

```
It runs a ruby script, ````/etc/sensu/plugins/ipmi-sensor-metrics.rb``` that gets sensor values and parses them in the following format before logging them into sensu.
```
moc-sensu.moc.ne.edu.ipmisensor.p1_temp_sens  37.0  1439569010
```
The influxdb handler inputs this parsed sensor data into influxdb.
