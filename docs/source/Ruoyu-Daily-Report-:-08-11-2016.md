Works I did today:

Modify ELK stack configuration in my staging environment.

Currently ELK stack is running normally on all nodes in production environment. We could collect all logs needed from all nodes, store them and visualize them. The problems are that logs from all compute nodes are not formatted. Besides, logs from controller nodes are formatted but not very well.

The targets are firstly parsing the log messages. Log messages from different services in openstack have similar format. For an example, below is a sample log message from nova service:

2016-08-08 17:59:44.150 5206 WARNING nova.compute.manager [req-b9d09438-40d8-4864-986e-f517a7a9cfe6 - - - - -] While synchronizing instance power states, found 8 instances in the database and 9 instances on the hypervisor.

The log message has the following format:
Date +  processID + Log level + module + request + logmessage

By writing regular expressions in filter and add the filter to logstash, we could parse the logs it collected, parse the log messages to nice JSON format. It will be helpful to our search and visualization in Kibana.

Secondly,  adding different ‘type’ label to different logs. It will also make our search and visualization in Kibana much more easier. For an example, all logs collected from /var/log/nova/ will be added “type:nova”.

Works to do tomorrow:

Finish testing new configurations of Filebeat and logstash and puppetize it.

I will try my best to finish writing and testing the new configuration in my staging environment. If I could get the right result. I will make a new pull request and puppetize the changes in the production environment.

Learning how to make different graphs in Kibana.

	I will look into different logs in openstack. Trying to find useful information that we need.