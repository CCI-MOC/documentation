# Use InfluxDB Handler in Sensu
This instruction is to tell you how to apply InfluxDB handler to insert data from sensu into the database for persistency so that we can process the data later, for example, displaying time series data in Grafana. 

Assuming Sensu and InfluxDB have been running without any issue, we will go through how to create the metric check json, how to tell the authentication info to the handler and apply the handler.

### Setup Influxdb handler
The handler consist of three files: the **handler program**, **json object**, and **parameters object**. The directory structure looks like this
```
/etc/sensu/conf.d/handler-influxdb-metrics.json
/etc/sensu/conf.d/influxdb-metrics.json
/etc/sensu/handlers/influxdb-metrics.rb
```

In `/etc/sensu/conf.d/influxdb-metrics.json`

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

In `/etc/sensu/conf.d/handler-influxdb-metrics.json`

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

`/etc/sensu/handlers/influxdb-metrics.rb` can be found on the sensu server we set up. 

There is also one from sensu-plugins-community, but we don't use it for it has network bug which InfluxDB community is working on. More about this can be referred this [issue](https://github.com/influxdb/influxdb-ruby/issues/91).

### Apply the metric check

Given you have the `check_memory_metric.rb` which can be easily found from the sensu-plugins-community, then put the file in `/etc/sensu/plugins/` folder. 

Then create the configuration json file for this check which tells Sensu sever the details of this check, for example the path of the program, check type, interval, and so on. We name it `check_memroy_metrics.json` and put it in `etc/sensu/conf.d/`.

The following is the content of `check_memroy_metrics.json`
```
{
  "checks": {
    "memory_metrics": {
      "handlers": ["default","influxdb"],
      "type": "metric",
      "command": "/etc/sensu/plugins/memory-metrics.rb",
      "subscribers": [
        "moc-sensu"
      ],
      "interval": 300
    }
  }
}
```

*NOTE: "type" must be specified, otherwise the check will not create event and the InfluxDB handler will not be aware of it and process it. After that restart the Sensu services to apply the changes.*

