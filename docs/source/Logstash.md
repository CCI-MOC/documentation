Logstash is an events and logs managing tool that is used to gather logging messages, convert them into json documents and store them in an Elasticsearch cluster. Kibana is used as a frontend client to search for and display messages from Elasticsearch cluster. Logstash can be used to collect, process, and forward events and log messages. 

### Configuration
Logstash configuration files consists of three parts: 

* Collection (Input) - accomplished via number of configurable input plugins including raw socket/packet communication, file tailing and several message bus clients.

* Processing (Filters) -  Once an input plugin has collected data it can be processed by any number of filters which modify and annotate the event data.

* Output - Finally events are routed to output plugins which can forward the events to a variety of external programs including Elasticsearch, local files and several message bus implementations.

The next code block gives the most basic config file, using the standard input from the terminal where you run logstash and outputs the messages to standard output.
```
input {
  stdin { }
}
output {
  stdout { }
}
```

Logstash stores the processed logs to elasticsearch like follows (set ES_private_IP to the elasticsearch's listening IP/port)
```
input { 
  stdin { } 
}
output {
  elasticsearch {
  host => "ES_private_IP"
  }
}
```

To collect logs from clients over the network:

To open an incoming TCP port on firewall, (wasn't persistent on reboot on redhat)
```
iptables -I INPUT -p tcp --dport 12345 --syn -j ACCEPT
```
Save changes by
```
service iptables save
#or on redhat run
iptables-save
```
This worked (made changes permanent) on redhat
```
firewall-cmd --permanent --zone=public --add-port=12345/tcp

firewall-cmd --reload
```


## Installation
```
sudo yum install java-1.7.0-openjdk
curl -L -O https://download.elastic.co/logstash/logstash/packages/centos/logstash-2.2.0-1.noarch.rpm
sudo rpm -i logstash-2.2.0-1.noarch.rpm
```

Don't start Logstash yet, because it still needs to be configured.

## Configuration
Because Beats sends events to Logstash, we need to install the Beats [Input Plugin for Logstash](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-beats.html). The Logstash home directory should be /opt/logstash From your Logstash home directory, run the following command. 

```
bin/plugin install logstash-input-beats
```

From your Logstash home directory create a configuration file. For example, the configuration file can be called ```config.json```. Copy the following to ```config.json```
```
input {
  beats {
    port => 5044
  }
}

output {
  elasticsearch {
    hosts => ["ES_host_ip:9200"]
    manage_template => false
    index => "%{[@metadata][beat]}-%{+YYYY.MM.dd}"
    document_type => "%{[@metadata][type]}"
  }
}
```

There doesn't seem to be a default path for the Logstash configuration file, because the configuration can be broken up into many files. For example, the input, filter and output configurations can all be their own file. All of the configuration files should go in the /opt/logstash/conf.d directory. Make sure you have at least one .conf file in this directory as well.

When you restart Logstash it doesn't always completely stop before it starts. If you make any changes to Logstash's configuration, be sure to kill all of the Logstash processes like so.

```
ps -ef | grep logstash 
sudo kill EACH_PID 
sudo kill -9 PID_THAT_WASNT_KILLED_BEFORE 
sudo /etc/init.d/logstash start
```
This way you can be sure Logstash has completely stopped.

## Testing the configuration

Logstash comes with a tool that allows you test your configuration. Go to the Logstash home directory and type in the following commands.

```
sudo systemctl stop logstash 
bin/logstash agent -f PATH_TO_LOGSTASH_CONF_FILE --debug
```

We need to stop Logstash first, because otherwise port 5044 will be in use. This means the configuration cannot be tested.

If you want to check the syntax of your configuration, run the following command.
```
bin/logstash agent -f PATH_TO_LOGSTASH_CONF_FILE --configtest
```

If you want to see all of the flags that can be used with this tool run ```bin/logstash --help```

Once the configuration has no errors, you can start Logstash again by running ```sudo systemctl start logstash```


## Logging
You can view the logs like so.
```
tail /var/log/logstash/logstash.log
```

Logstash archives logs so if you go to the /var/log/logstash directory and view the log of your choosing. Some of the logs my be compressed, so you would need to run ```gunzip LOG_NAME``` to view it.
 
### Generate SSL Certificates

We need SSL certificate and key pair in order to use Logstash Forwarder to ship logs from our Servers to our Logstash Server. The certificate is used by the Logstash Forwarder to verify the identity of the Logstash Server.

(There's also a different way of doing this if you have a DNS setup) Here, we add the IP address of the Logstash Server to the subjectAltName (SAN) field of the SSL certificate that we are about to generate. To do so, open the OpenSSL configuration file:
```
$ vi /etc/pki/tls/openssl.cnf
```
Find the [ v3_ca ] section in the file, and add this line under it (substituting in the Logstash Server's private IP address):
```
subjectAltName = IP: logstash_server_private_ip
```

Generate the SSL certificate and private key in location (/etc/pki/tls/), with the following command
```
$ cd /etc/pki/tls
$ sudo openssl req -config /etc/pki/tls/openssl.cnf -x509 -days 3650 -batch -nodes -newkey rsa:2048 -keyout private/logstash-forwarder.key -out certs/logstash-forwarder.crt
```

Inorder for Logstash Server to collect logs shipped by Logsash Forwarder, the input section of the logstash .conf file must be configured similar to the following:
```
input {
  lumberjack {
    port => 5000
    type => "logs"
    ssl_certificate => "/etc/pki/tls/certs/logstash-forwarder.crt"
    ssl_key => "/etc/pki/tls/private/logstash-forwarder.key"
  }
}
```
This specifies a lumberjack input that will listen on TCP port 5000, and it will use the SSL certificate and private key that we created earlier.