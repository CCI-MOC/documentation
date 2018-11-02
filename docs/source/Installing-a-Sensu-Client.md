This wiki page explains you how to install a Sensu client, to monitor a machine. The Sensu client always talks to sensu-server to execute the checks and handlers, which is told in the form of subscriptions. The Sensu-server(moc-sensu) lives on Node:55, the following commands takes you to moc-sensu.    
```
ssh -A username@129.10.5.39
ssh username@10.13.37.98
      (or)
ssh -A username@129.10.5.55

```

## Create a Sensu Core Repository 
Create the YUM repository configuration file for the Sensu Core repository at ```/etc/yum.repos.d/sensu.repo```
```
echo '[sensu]
name=sensu
baseurl=http://repositories.sensuapp.org/yum/$basearch/
gpgcheck=0
enabled=1' | sudo tee /etc/yum.repos.d/sensu.repo
```

## Install Sensu Core
Install Sensu Core from the Sensu repository:
```
sudo yum install sensu
```

## Configure the Sensu Client
Each sensu client requires its own client definition, containing a set of required attributes (name, address, subscriptions). To configure the sensu client, open the client configuration file at ```/etc/sensu/conf.d/client.json```. If the directory is not created by the sensu installer create this directory manually by 
```mkdir /etc/sensu/conf.d```
 
An example is below:
```
{
  "client": {
    "name": "compute-16.moc.ne.edu",
    "address": "10.13.37.67",
    "subscriptions": [
      "moc-sensu"
    ]
  }
}
```

Sensu use RabbitMQ to communicate between server and client and viceversa. In order for the client to communicate with server, we should have the address of the sensu-server which has RabbitMQ running. If the client needs to be monitered by the moc-sensu(production sensu) which is an example of distributed configuration, copy the following contents to ```/etc/sensu/config.json```

```
{
  "rabbitmq": {
    "host": "10.13.37.98",
    "vhost": "/sensu",
    "user": "sensu",
    "password": "secret"
  }
}
```
If the client is in the staging environment, sensu-server and client lives on the same machine which is an example of standalone configuration, then change the host to ```localhost```

Ensure the Sensu configuration files are owned by the Sensu user and group ```sensu```:
```
sudo chown -R sensu:sensu /etc/sensu
```

## Configure a check 
Sensu checks allow you to monitor server resources, services, and application health, as well as collect & analyze metrics. These are scripts that are executed on the client machines, but defined on the server machines.
Client will execute the checks based on the subscriptions it is subscribed from the sensu-server, these can be seen in the the subscriptions attributes in the client.json file. Checks are configured on the Sensu server. 

The plugins (scripts that executes these checks) for the checks can be found in the Sensu community. **Be sure to see if someone already created a plugin for your use case before you try writing your own**. You can browse all of the plugins for checks [here](https://github.com/sensu-plugins)

Checks are configured on the Sensu server. There are many checks already configured on the Sensu server, so you may not need to configure all of your checks. If the check configurations are not already on the Sensu server, you need to configure them. All check configurations go in the ```/etc/sensu/conf.d/``` directory on the Sensu server.  For example, the check for measuring CPU metrics is configured at  ```/etc/sensu/conf.d/hardware``` on the Sensu server like so:
```
{
  "checks": {
    "cpu_metrics": {
      "handlers": ["default", "influxdb-cpu"],
      "type": "metric",
      "command": "/etc/sensu/plugins/cpu-metrics.rb",
      "subscribers": [
        "moc-sensu"
      ],
      "interval": 30,
      "handler": "debug"
    }
  }
}
```

## Install a plugin
The sensu-plugins can be installed using gem on the Sensu client like so.
```
gem install sensu-plugin (ex: gem install sensu-plugins-disk-checks)
```

All plugins that are executed on the client, go into ```/etc/sensu/plugins/``` directory. Here while installing we have to use system ruby not the ruby version installed by the sensu(embedded ruby). To disable the option of using embedded ruby go to ```vi /etc/default/sensu``` and do```EMBEDDED_RUBY=false```.  

As the CPU metrics check is already configured, assuming the cpu-plugins are installed using gem we can run the script to see what it displays.

```
ruby /etc/sensu/plugins/cpu-metrics.rb
```

To learn more about checks, you can refer to Sensu's documentation on checks [here](https://sensuapp.org/docs/latest/checks)

## Run the Sensu client
To start the Sensu client, run the following command.
```
sudo /etc/init.d/sensu-client start
       (or)
sudo service sensu-client start
       (or)
sudo systemctl start sensu-client

```

## Verify the Sensu client.
As of now we know how to configure a sensu client, configure a check and install plugins. To verify if everything goes as expected, go to Sensu client log file. 
```
sudo tail -f /var/log/sensu/sensu-client.log

```

## See the client on the dashboard
To monitor the status of the Sensu clients, we use Uchiwa as a dashboard. To login to the dashboard,use following IP address and port in your browser http://129.10.5.55:3000/

The credentials to login are as follows:

Username: ```admin``` Password: ```MOCmonitor2#```

To see all of the clients being monitored, click on the second icon down on the left side of your screen. You can also click this [link](http://129.10.5.55:3000/#/clients). There you should see the client you just configured.

## Configuring a handler
A handler allows Sensu to take action on events that are produced by your check results. These can be used for sending alert emails, storing metrics in InfluxDB and more. 

Handers are configured on the Sensu server. Handlers consist of three files, a definition file, a parameters file, and the handler itself. The handler definition and parameters files are in the ```/etc/sensu/conf.d/handlers```, where as the handler itself is stored in the ```/etc/sensu/handlers/```

An example of a handler definition file is as follows:
```
{
  "handlers": {
       "influxdb-cpu": {
           "type"        : "pipe",
           "command"     : "/etc/sensu/handlers/influxdb-cpu-metrics.rb"
     }
  }
}
```
An example of a handler's parameters file is as follows:
```
{
  "influxdb-cpu": {
    "host": "localhost",
    "port": 8086,
    "username": "root",
    "password": "root",
    "database": "sensu_db"
  }
}
```
An example of a hander can be found [here](https://github.com/LeonLee88/moc_sensu_server/blob/master/handlers/influxdb-cpu-metrics.rb)

When ever we add any changes to the configuration file of the checks, you must restart the sensu server in order for the handler or checks to start working.

On the server run the following command:
```
sudo service sensu-server restart 
```

If the client attributes are changes in the client.json file, then restart sensu-client:
```
sudo service sensu-client restart
```

After sometime, you should see the data from your checks going into InfluxDB, if u have added InfluxDB handler. To query InfluxDB go to http://10.13.37.179:8083. A measurement in InfluxDB is synonymous with a table in any relational database.

To learn more about handlers, you can refer to Sensu's documentation on handers [here](https://sensuapp.org/docs/latest/handlers).

## Issues communicating with sensu-server
When ever we have issues communicating with sensu-server we need to verify only two files. The first one is 
```vi /etc/sensu/conf.d/client.json``` and second one is ```vi /etc/sensu/config.json```.

Example client.json file.

{
  "client": {
    "name": "NAME_OF_THE_CLIENT",
    "address": "IP_ADDRESS_OF_THE_CLIENT",
    "subscriptions": [
      "SUBSCRIPTION_1",
      "SUBCRIPTION_N"
    ]
  },
    "keepalive": {
      "thresholds": {
        "warning": 60,
        "critical": 300
      },
      "handlers": ["node-email"],
      "aggregate": true,
      "refresh": 3600
   }
}
```

Now you need to configure which RabbitMQ instance your Sensu Client communicates with. Assuming that the Sensu server is running, change the configuration file at ```/etc/sensu/config.json``` as shown below.
```
{
  "rabbitmq": {
    "host": "SENSU_SERVER_IP_ADDRESS",
    "vhost": "/sensu",
    "user": "sensu",
    "password": "secret"
  }
}
```

Ensure the Sensu configuration files are owned by the Sensu user and group ```sensu```:
```
sudo chown -R sensu:sensu /etc/sensu
```

In order for your changes to take effect, you must restart the Sensu client. You can do this by running the following command. 
```
sudo service sensu-client restart
```

You should see the Sensu client in your Uchiwa dashboard on your Sensu server. If you do not see you client, restart all of the Sensu services on your Sensu server like so.

```
sudo service sensu-server restart
sudo service sensu-api restart
sudo service sensu-client restart
```

If you don't want to change any of the checks or handlers on your Sensu client then you are done! If your need change the checks or handlers, refer to the "Configure a Check" or "Configure a Handler" sections of this document and create and delete the appropriate files. 

## Additional resources
You can now configure a Sensu client, check and handler! Here are some additional resources that are useful in learning more about Sensu and how we use it in our production environment.

Handlers:
* [Getting started with handlers](https://sensuapp.org/docs/latest/getting-started-with-handlers)
* [Handlers Documentation](https://sensuapp.org/docs/latest/handlers)
* [Our Wiki page on InfluxDB Handlers](https://sensuapp.org/docs/latest/getting-started-with-checks)

Checks:
* [Getting started with checks](https://sensuapp.org/docs/latest/getting-started-with-checks)
* [Checks Documentation](https://sensuapp.org/docs/latest/checks)
* [Sensu Plugins for your checks](https://github.com/sensu-plugins) 

Sensu in our Production Environment:
* [A repository with our Sensu server code](https://github.com/LeonLee88/moc_sensu_server)
* [Overview of Sensu in our production environment](https://github.com/CCI-MOC/moc/wiki/Sensu)
* [Monitoring in our NEU environment](https://github.com/CCI-MOC/moc/wiki/Monitoring-in-NEU-Env.)
* [Accessing our monitoring components](https://github.com/CCI-MOC/moc/wiki/Access-monitoring-components)
