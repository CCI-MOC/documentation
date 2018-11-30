# InfluxDB Crash Course
This wiki page will introduce the reader to influxDB and explain how to query our production influxDB in its current state as of July 18, 2016.

### InfluxDB quick introduction
InfluxDB is a time-series database. Our sensu server collects data from our compute nodes and stores that data in influxdB. every datapoint gets a timestamp.

Currently, our production influxDB is located at ip address `10.13.37.179`.

To query the influxDB, you need to connect to the 10.13 subnet (connect with X2Go client to node 39 or configure Firefox proxy settings) and then go to address `http://10.13.37.179:8083/` in a browser

*Always remember: Change the database in the top right to sensu_db*

Now, you are ready to query our database. We will start off simple and then create more complicated queries.

### Queries
Type in `SHOW MEASUREMENTS` and press enter. This will give all the tables in the database. You will use these table names in future queries.

So, now that we can see all the table names, lets pick memory_metrics and see what it looks like.

Type  `SELECT * FROM memory_metrics ORDER BY time desc LIMIT 10`

This will return the last 10 data points from memory metrics. the time desc option makes sure you return the most recent data points. You can see what this table looks like now. 

*Make sure you always put a limit, or it will return the whole table, which is huge and will take too long.*

Lets look at all data points in the last 5 minutes.

`SELECT * FROM memory_metrics WHERE time > now() - 5m`

Now, lets look at all data points in the last 5 minutes from a particular host

`SELECT * FROM memory_metrics WHERE time > now() - 5m AND "host" = 'compute-34.moc.ne.edu'`

Now, if we want to choose the just value and the time:

`SELECT value,time FROM memory_metrics WHERE time > now() - 5m AND "host" = 'compute-34.moc.ne.edu'`

Cool. Now, let's select the average of all values in the past 5 minutes for the metric "used_percentage" for compute-34.moc.ne.edu:

`SELECT mean(value) FROM memory_metrics WHERE time > now() - 5m AND "metric" = 'used_percentage' AND "host" = 'compute-34.moc.ne.edu'`

So, what does this number returned mean? This number is the average memory percentage used by compute 34 in the past 5 minutes!

*protip: the querying language in influxDB is very specific. Make sure you do not leave any seemingly irrelevant syntax out while querying. 

For example, you must say "metric" = 'used_percentage'. "metric" = used_percentage or "metric" = "used_percentage" will return nothing. Those single quotes are necessary.*

All right, now that we've got that stuff down, lets look at a more complicated table: The `Aggregated_metrics` table

Just to get a glimpse of what it looks like, lets do a basic query:

`SELECT * FROM Aggregated_metrics ORDER BY time desc LIMIT 10`

Now we can see all of the relevant columns in this table. The table is combining many tables together, so the measurement column keeps track of the table they came from.

As two final queries to this guide, let's query the Aggregate table for the average CPU utilization among all hosts 30 minutes ago to 20 minutes ago, and then among a few specific hosts from 30 minutes to 20 minutes ago:

First, all hosts:

`SELECT mean(value) FROM Aggregated_metrics WHERE time > now() - 30m AND time < now() - 20m AND "measurement" = 'cpu_pcnt_metrics' AND "metric" = 'used_percentage'**`

Now, a few hosts:

`**SELECT mean(value) FROM Aggregated_metrics WHERE time > now() - 30m AND time < now() - 20m AND "measurement" = 'cpu_pcnt_metrics' AND "metric" = 'used_percentage' AND ("host" = 'compute-34.moc.ne.edu' OR "host" = 'compute-20.moc.ne.edu' OR "host" = 'compute-21.moc.ne.edu')**`

