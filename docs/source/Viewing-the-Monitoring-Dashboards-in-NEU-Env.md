# Viewing the Monitoring Dashboards in NEU Env.
If you want to view the monitoring there are some dashboards that you can view. This page will show how you can make good use of these dashboards.

### Sensu Monitoring Dashboards

**Uchiwa**

Sensu's primary dashboard is called Uchiwa. To view the dashboard go to [http://129.10.5.55:3000/](http://129.10.5.55:3000/).

Before you can view the dashboard, you need to login. The credentials are as follows.
```
Username: admin
Password: MOCmonitor2#
```

The first page you see will summarize all of the events. The navigation bar is on the side. You can click on events, and clients to see more information about each one.

**Grafana**

Grafana is how we visualize all the metrics we collect. It uses InfluxDB, which is a time series database. This means each record has a timestamp associated it.

To view the dashboard got to [http://129.10.5.55:3033/](http://129.10.5.55:3033/). Before you can view the dashboard, you need to login. The credentials are as follows.

```
Username: admin
Password: MOCmonitor2#
```

The first page you will see will list all of the dashboards. You can click on a dashboard to view it. Each graph consists of one or more queries that are being run every couple of seconds.

To graph a different metric that we measure you can either, edit a graph or create a new graph with your query.

**InfluxDB**

InfluxDB is the time series database that Grafana uses. The admin interface can be used for ad-hoc queries.

For information on how to view the database, go [here](InfluxDB-Crash-Course-Querying-Data.html)

### ELK Stack Monitoring Dashboard

**Kibana**

Kibana is a dashboard for searching and visualizing log files from many hosts. The interface can be used for debugging issues that may have affected more than one host.There are no login credentials but you need configure your browser to do tunneling.

In Firefox do the following steps to view the Kibana Dashboard:
* Go to the Preferences tab
* Click "Advanced"
* Click "Network"
* Click Settings
* Select manual proxy configuration
* Enter `localhost` in the SOCKS Host field
* Enter a four digit port number in Port field
* Run the following command in your terminal `ssh -D PORT_NUMBER USERNAME@129.10.5.39`
* Go to [http://10.13.37.99:5601/](http://10.13.37.99:5601/)

Alternative if you do not want to change the firefox settings:
 * `ssh -L PORT_NUMBER:10.13.39.99:5601 USERNAME@129.10.5.39`
 * Go to `http://localhost:PORT_NUMBER` in your browser

