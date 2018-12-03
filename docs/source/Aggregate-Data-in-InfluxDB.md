# Aggregate Data in InfluxDB
This wiki page contains an explanation of how we aggregate data from different measurements (tables) in influxDB and put them into one measurement(table). 

The purpose of this page will be to explain a script that I have written. The script is located on the kaizen-sensu-admin, located at 10.13.37.179 in the `/root` directory.

You can also find the script [here](https://github.com/CCI-MOC/moc/blob/master/scripts/influxdb_aggregate)

In addition, this script pulls from a python dict to do some of its aggregates. That dict is also in the /root directory of 10.13.37.179, and can be found [here](https://github.com/CCI-MOC/moc/blob/master/scripts/switch_network_table)

I recommend having the production influxDB open while following this guide, so you can see what the actual tables look like.

I have also written a guide on basic influxDB commands so you can figure out [how to query this aggregate table and view it](InfluxDB-Crash-Course-Querying-Data.html)

### InfluxDB and measurements
InfluxDB is a time-series database that we use to store data from Sensu and Ceilometer. It contains tables of data, but refers to these tables as measurements. From here on out we will just say tables. 

### InfluxDB aggregate script: The basics
InfluxDB has a fairly limited query language at the moment, as it is relatively new. 

Because of this, there is not much support for merging different tables into one. So, we've had to write a script that does this.

The `influxdb_aggregate` script is run once every minute by a cronjob.

The script queries influxDB for certain tables, and certain metrics within those tables. It calculates an average of all the data points received in the last minute for each specific metric and host, and puts them into a new table, called "Aggregated_metrics".

For example, It queries the table "memory_metrics" for all data in the past minute. It sees that the host `compute-39.moc.ne.edu` has given 3 points of data for the "used_percentage" metric. 

It will average these three data points and put it into the aggregated table with the metric being "used_percentage" and the host being "compute-39.moc.ne.edu".

The only exception to this is for metrics "reads" and "writes". For these, we take the sum instead of the average.

I mentioned earlier that the script uses a dictionary for some of the aggregates. It uses this dict for the switch_network table. This is because the switch_network table is confusing, because instead of giving us the actual host name in the table, it gives us the switch name and the interface. 

We want to know the host name, so the table is just a mapping between switch/interface and host name. 

For example, switch 19 with interface 1/1 is compute-25.moc.ne.edu. Switch 17 with interface 1/1 is compute-1. So, the aggregates script uses this mapping and writes the host name based on the dictionary.

This is the basic description of how the aggregates script works. It queries tables for their data and writes that data into an aggregated table.

### InfluxDB aggregate script: The code
Now that we know the general idea of how the script works, we will walk through some of the nuances of the code.

The main() function of the code is pretty simple. It defines a source ("Sensu" or "Ceilometer), a category ("Memory", "CPU", etc.) a table name ("memory_metrics") and the metrics from that table we want to collect ("free", "total"). Then, it calls the aggregates script.

If your checks are from Sensu and you are using the same influxDB handlers we have been using in production, this should just work.

Without having to look at the code at all, if your tables look identical to tables such as the memory_metrics table or the cpu_pcnt_metrics table, and have all the relevant columns, this should successfully insert them into the aggregates table.

If your tables differ, you will have to write some of your own code to get them inserted. You can either use the same aggregate function that exists, and then in the function write something like (if measurement == "your table name"), or you can write a new function that will be called for your specific mesaurement/metric combo. 

Currently, the code will start a double loop, so that it can loop through each metric and each host for each metric. Then, for that particular table, it selects the average value of all the data points in a minute range for each host/metric. 

This differs slightly for the switch_network table, which does a triple loop (metric, host, interface - triple). Some measurements differ very slightly, such as the ipmi sensor metrics, which has the table "sensor" instead of metric, and you will see some if statements in the code for cases like these.

After the script selects the data, it will write it into a file. Then, when the script is finished, it will write all points in the file into influxDB. This will speed up the script significantly and is necessary for the script to successfully run in under a minute.

### Debugging the script
If you are editing the script and need to debug, the first thing you should do is stop the cronjob by going to `/etc/crontab` and commenting out the line that refers to the aggregate script.

Afterwards, you can edit the script and run it and debug as you would normally. You can also comment out the lines about inserting the file into influxDB. Those lines are located at the end of the script.

