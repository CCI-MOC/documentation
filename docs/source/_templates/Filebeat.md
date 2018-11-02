Filebeat is responsible for forwarding the logs on all of the clients to either Logstash or Elasticsearch. In our configuration, Filebeat is forwarding all of the logs to Logstash. Filebeat must be installed on every machine that will have their logs monitored.

## Installation

Download and install Filebeat by running the following command.
```
curl -L -O https://download.elastic.co/beats/filebeat/filebeat-1.1.0-x86_64.rpm
sudo rpm -vi filebeat-1.1.0-x86_64.rpm
```

## Configuration
Edit the /etc/filebeat/filebeat.yml so it looks like the following.
```
############################# Filebeat ######################################
filebeat:
  # List of prospectors to fetch data.
  prospectors:
    # Each - is a prospector. Below are the prospector specific configurations
    -
      # Paths that should be crawled and fetched. Glob based paths.
      # To fetch all ".log" files from a specific level of subdirectories
      # /var/log/*/*.log can be used.
      # For each file found under this path, a harvester is started.
      # Make sure not file is defined twice as this can lead to unexpected behaviour.
      paths:
        - "/var/log/*.log"
        - "/var/log/secure"
        - "/var/log/messages"
      # Type of the files. Based on this the way the file is read is decided.
      # The different types cannot be mixed in one prospector
      #
      # Possible options are:
      # * log: Reads every line of the log file (default)
      # * stdin: Reads the standard in
      input_type: log

      # Name of the registry file. Per default it is put in the current working
      # directory. In case the working directory is changed after when running
      # filebeat again, indexing starts from the beginning again.
      registry_file: /var/lib/filebeat/registry
output:
  ### Logstash as output
  logstash:
    # The Logstash hosts
    hosts: ["ES_host_ip:5044"]
shipping:
logging:
  files:
    rotateeverybytes: 10485760
```

Keep in mind this is the canonical filebeat.yml file. This forwards all of the logs with messages, secure and any log that ends with ".log". If you want to forward Openstack logs you need to add all of the paths for the logs for each Openstack service. For example the paths for the filebeat.yml on node 25 are as follows:

```
paths:
  - "/var/log/*.log"
  - "/var/log/secure"
  - "/var/log/messages"
  - "/var/log/neutron"
  - "/var/log/nova"
  - "/var/log/cinder"
  - "/var/log/glance"
  - "/var/log/horizon"
  - "/var/log/keystone"
```

## Start Filebeat
Run the following command to start Filebeat.
```
sudo /etc/init.d/filebeat start
```
