# Loggin IPMI Data to Sensu and InfluxDB
Accessing the Cisco servers is explained [here](Cisco-UCS-C220.html) in detail.

### IPMItool Installation
You can access IPMI functionality through the command line with the IPMItool utility. To be able to use `ipmitool`, install it with the following
```
$ yum install OpenIPMI OpenIPMI-tools
$ chkconfig ipmi on
$ service ipmi start
```

You can also locate IMPItool and its related documentation on your Tools and Drivers CD image, or [download this tool](http://ipmitool.sourceforge.net/)

After you install the IPMItool package, you can access detailed information about command usage and syntax from the man page that is installed. From a command line, type `man ipmitool`

### Connecting to the Server With IPMItool
To connect over a remote interface you must supply a user name and password. The default user with admin-level access is root with password changeme. 

On the NE cluster, the username and password credentials for the Cisco server OBMs are provided in the command `ipmitool -I lanplus -H 10.99.1.101 -U admin -P 73qtx8nVXa4c06 chassis status`

### Using IPMItool to Read Sensors
To get a list of all sensors in these servers and their status, use the `sdr list` command with no arguments. This returns a large table with every sensor in the system and its status.

The four fields of the output lines, as read from left to right are:
1. **IPMI sensor number**
2. **IPMI sensor ID**
3. **Sensor reading**
4. **Sensor status**, indicating which thresholds have been exceeded.

For example:
```
2 | /SYS/SLOTID | 0x02 | ok

3 | HOSTPOWER   | 0x02 | ok

4 | CMM/PRSNT   | 0x02 | ok

5 | PEM/PRSNT   | 0x02 | ok
```
This [doc](https://docs.oracle.com/cd/E19464-01/820-6850-11/IPMItool.html) provides additional information including reading specific sensors.

### Logging sensor readings into Sensu
After parsing the sensor data into "<host.sensor-name>  <value>  <timestamp>" format using this [script](https://github.com/sensu/sensu-community-plugins/blob/master/plugins/ipmi/check-sensor.rb), a sensu check picks up the output and logs it into the sensu server.
```
{
  "checks":{
    "sensor_check":{
      "handlers": ["default","influxdb"],
      "type": "metric",
      "command": "/etc/sensu/plugins/check-sensor-t.rb -u admin -p 73qtx8nVXa4c6 -h 10.99.1.102",
      "interval": 30,
      "subscribers": [ "moc-sensu" ]
     }
  }
}
```

The influxdb handler stores the sensor data in inflxdb.

