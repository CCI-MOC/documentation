# Monitoring in NEU Env.
Now we have four VMs set up for monitoring in NE environment. Three VMs are set up on compute node 39, and one VM is set up on Haas master to collect data from IPMI.

In order to log into those machines, you will first need send an email to Hua Li(lihua88@bu.edu) with your public ssh key and your preferred username on the VMs. He will add you as a user of the VMS.

### Monitor VMs on Haas Master
We've set up a sensu on a VM that is located on Haas Master. This VM have access to the IPMI network. 
1. **Log on to Haas Master**

    You can click [here](Accessing-Northeastern-Cluster.html) for information about logging into Haas Master.

2. **Log on to the VM**
```
$ ssh -A <username>@192.168.122.147
```
Now this VM is not set up with the network that can talk to our database -- influxdb. We need to do more configuration on this.

### Monitor VMs on compute 39

**Sensu, InfluxDB, Grafana**

1. **Log on to the VM**
    `ssh <USERNAME>@129.10.5.55`
2. **Uchiwa Dashboard**

    Uchiwa Dashboard shows all the checks of sensu and the status of the services/physical nodes that Sensu is monitoring. The webpage is ```129.10.5.55:3000```

    The username and password is in uchiwa.json. Username is ```admin``` and password is ```MOCmonitor2#```.

3. **InfluxDB Dashboard**

    InfluxDB is the database in which data collected by Sensu is stored. You can visit it at ```129.10.5.55:8083```

    Username ```root```, Password ```root```, Port settings ```8086```

4. **Grafana**

    Grafana is the visualization tool which utilize the data in InfluxDB directly. You can visit it at ```129.10.5.55:3033```
    ```
    Admin user: username: admin Password:MOCmonitor2#
    Guest user: username: moc_guest Password:moc_guest
    ```
    More about sensu, influxDB, and Grafana can be viewed here: [Sensu](Sensu.html),[InfluxDB & Grafana](Influxdb-%26-Grafana.html)

**Logstash, ElasticSearch and Kibana**

1. **Log on to the VM**
    `ssh -A <username>@10.13.37.99`
2. **Check the status**

    Instances of logstash and elasticsearch running on screen. To list all the screens,  run ```screen -ls```.

    To list a running screen, run ```screen -x <screen name>```
    *Note: No need to do this unless to check if they’re running, restart them with different config or stop them*

3. **Check if elastic search is running**
    `curl 10.13.37.99:9200`
4. **Kibana**

   Kibana is the dashboard for the logs. It can be visited at `10.13.37.99:5601`


### Ceilometer
1. **Log on to the VM**
    `ssh <USERNAME>@129.10.3.56`
2. **MongoDB**

    Ceilometer use MongoDB as its default database. You can access MongoDB using the following command `mongo 10.13.37.91` (ip address is needed)

    More about Ceilometer Installation can be viewed [here](Ceilometer-Installation-in-NE-Env..html)
 
### Log on to VMs through compute 39
You can also log on to the VMs first by logging on to compute 39 and then use the internal IPs of each VM. But it is not recommended.
`ssh -A -D <PORT#> <USERNAME>@129.10.3.39`
`ssh -A <USERNAME>@<vm.internal.ip>`

**Internal IPs are** :
* **moc-ceilometer** : 10.13.37.91
* **moc-sensu** : 10.13.37.98
* **Logstash** : 10.13.37.99

