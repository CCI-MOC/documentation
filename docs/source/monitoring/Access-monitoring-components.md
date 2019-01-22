## Sensu
**Sensu VM on node 39**

Via 10.13.37.X subnet:

     [lihua@localhost ~]$ ssh -A username@129.10.3.39 # Login 39 Node
     [lihua@compute39 ~]$ ssh -A username@10.13.37.98 # Login Sensu VM

Via public IP:

     [lihua@localhost ~]$ ssh username@129.10.5.55

**Sensu-ipmi on Haas master**

     [lihua@localhost ~]$ ssh -A lihua@129.10.3.48 # Login Haas master
     [lihua@haas-master ~]$ ssh -A lihua@192.168.122.147 # Login Sensu-ipmi VM

**Sensu dashboard - Uchiwa**

Uchiwa is listening to port 3000. Interface can be accessed via browser using http://sensuserver:3000.
 
     http://129.10.5.55:3000 # Via public IP

## InfluxDB

**InfluxDB web interface**

InfluxDB web interface is listening on port 8083 and the server service is listening on port 8086:

     http://129.10.5.55:8083 # username:root password:root

## Grafana

Grafana runs on sensu VM and is listening on port 3033.

    http://129.10.5.55:3033 # username:admin password:MOCmonitor2#