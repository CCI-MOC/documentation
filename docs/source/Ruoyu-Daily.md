# 12/22/2016
Works I did today:
Today I read through the wiki docs I wrote. Checked and verified the the process of store&visualize rally report. Made some modifications on these.

Works to do tomorrow:
Continue working on writing wiki docs about my work.

# 12/21/2016
Works I did today:

1. Write a cron job which will run rally test and then parse the test result to a log file periodically.
2. Collect, parse, store and visualize the log file through ELK stack.
3. Helped Shashank debug ceilometer in his staging environment.
4. Wrote wiki document about how to set up ceilometer.

Works to do tomorrow:

1. Refine the process of store&visualize rally report.
2. Write wiki doc.

# 12/20/2016
Works I did today:

1. Wrote wiki document about how to set up ceilometer.
2. Wrote a python script. This script could read rally results which is a json file, grep the important information like test name, duration and write it to a log file. 
3. Set up a ELK stack in my vm. Let filebeat to collect the log file which contains rally results. And logstash filter the file, store it in elasticsearch. Also installed kibana for future visualization.

Works to do tomorrow:

1. Run rally results periodically. And also run python script to parse it.
2. Visualize the data collected in Kibana.
3. Continue working on documents about Ceilometer.

# 12/19/2016
Works I did today:

Today I mainly wrote documents on Moc wiki page about Kibana and Ceilometer. Finished the Kibana page called "How to Use Kibana". Currently I am working on a new page called "Ceilometer Deployment Guide”. Which shows how I deployed ceilometer in my staging environment.

Works to do tomorrow:

1. Continue working on documents about Ceilometer.
2. Research how to visualize rally report.

# 12/15/2016
Works I did today:

1. Researched how to insert Rally reports in elasticsearch and visualiz it in Kibana.

2. Wrote document on Moc wiki page about elasticsearch.

Works to do tomorrow:

1. Continue wrote document about Logstash, Kibana, and Ceilometer.

2. Continue looking into visualizing rally report.

# 12/14/2016
Works I did today:

1. Read documents about Gnocchi and practiced installing Gnocchi database in one of my VM.

2. Read tutorials about MongoDB.

Works to do tomorrow:

1. Continue learn MongoDB.

2. Practice fetching ceilometer data from mongoDB database.

3. Test ceilometer performance.

# 12/13/2016
Works I did today:

Today I researched how to get network meters in ceilometer so that we could get network usage data. Currently we could get cpu, memory, disk, vcpus meters. I have tried several ways but we still not get meters related to networks. I will continue working on this issue.

Works to do tomorrow:

1. Research how to get neutron networking meters.

2. Install Gnocchi database to replace mongodb as ceilometer backend database.

3. Visualize ceilometer data through grafana.

# 12/12/2016
Works I did today:

Today I solved ceilometer bug. The reason is that the information in "service_credentials" part of ceilometer.conf is wrong. In ceilometer.conf file "service_credentials" part, I wrongly configured os_username, os_password, os_tenant_name, os_auth_url to be ceilometer service credentials. Which should be openstack services credentials that could access all services. That's why ceilometer cannot talk to nova api. The credentials is wrong.

Now we could get cpu, memory and disk usage data from ceilometer CLI. And the error disappears. 

Works to do tomorrow:

1. Install Gnocchi database to replace mongodb as ceilometer backend database.

2. Visualize ceilometer data through grafana.



# 12/09/2016
Works I did today:

Today I continued looking into ceilometer bug. Build a packstack node on one of my vm. It is a all in one openstack deployment for practicing and testing openstack. Ceilometer is deployed automaticly on this node by puppet. Will verify ceilometer on this node.

Works to do next week:

1. Verify and practice using ceilometer in the vm.
2. Continue looking into the ceilometer bug. Try to solve it.

# 12/08/2016
Works I did today:

Today I continued looking into ceilometer bug. I worked with Jeremy and shashan on this issue. Still don’t find a way to solve it. But I will continue dig into this problem.

Works to do tomorrow:

Continue looking into the ceilometer bug. Try to solve it.

# 12/07/2016
Works I did today:

1. Today I mainly looked into ceilometer issue in my staging. Filed a bug report to red hat Bugzilla.

2. Watched some vedios and documents about openstack ceilometer and nova.

Works to do tomorrow:

1. Continue looking into the ceilometer bug.

2. Continue learning openstack.

# 12/05/2016
Works I did today:

Today I helped on modifying and submitting the posters. Also I read some documents and watched videos about openstack services.

Works to do tomorrow:

Attend moc workshop to help people know more about our group and moc. Also attend some sessions.

# 12/02/2016
Works I did today:

1. Today I mainly worked on moc workshop posters. We will continue doing it through the weekend and hopefully will accomplish it on Monday.

2. Read some documents and watched videos about ceilometer.

Works to do next week:

1. Continue working on posters and slides.

2. Continue debugging ceilometer.

# 12/01/2016
Works I did today:

1. Worked on workshop posters. Create slides that covered ELK stack and ceilometer part.

2. Researched the ceilometer issue. Modified ceilometer configuration files in the compute nodes.  Still got the error that ceilometer compute agent couldn’t not connect to nova client to poll data.

Works to do tomorrow:

1. Continue working on posters and slides.

2. Continue debugging ceilometer.

# 11/30/2016
Works I did today:

1. Researched how to get ip address from the logs collected. Currently filebeat is not collecting httpd logs. We may need to modify filebeat puppet code on the controller to enable it.

2. Debugged ceilometer. Ceilometer uses two methods to collect data: a. Notification Agents: Listening for data. b. Polling Agents: Asking for data

The polling agent is not working properly now. We got the errors that "ceilometer.nova_client ConnectionError: ('Connection aborted.', BadStatusLine("''",))" and don't get user resource usage data. Will continue working on this issue.

Works to do tomorrow:

1. Continue debugging ceilometer.

# 11/29/2016
Works I did today:

Today I mainly researched and debugged ELK stack. Let elasticsearch reallocated all shards. ELK stack back to work now. The speed is similar to what we have initially.

Works to do tomorrow:
1. Research how to get ip address from the logs collected.
2. Research ceilometer use cases.

# 11/28/2016
Works I did today:

1. Debug and configured Elasticsearch. Set java heap size to be half of the memory. Reallocated shards.

2. Researched ways to improve ELK stack. 

Works to do tomorrow:

1. Continue debugging elk stack. Make it running.

2. Research how to get ip address from the logs collected.

# 11/23/2016
Works I did today:

1. Gathered tempest full test results and generated report.

2. Setted up Kibana and logstash to work for the cluster.

Works to do for next week:

1. Setting up and testing ELK stack cluster in my environment.

2. Deploy the cluster to the production.

3. Get ip address from the logs collected.

# 11/22/2016
Works I did today:

1. Practiced setting up elasticsearch cluster. I  created 3 instance in Kaizen, installed and configured elasticsearch on each nodes to form a cluster.
2. Debugged in the cluster and reading documents about how to manage the cluster.

Works to do tomorrow:

1. Setting up Kibana, logstash and filbeat to work for the cluster.
2. Finish gathering tempest full test results and generate report.


# 11/21/2016
Works I did today:
1. Run tempest full test in my staging for ceilometer on and off.
2. Debug in my staging. Solved could not run openstack command problem. By adding more memorys to the hosts.
3. Learned how to set up elastic cluster and extend logstash.

Works to do tomorrow:
1. Continue setting up elastic cluster in the staging to practice extend node.
2. Get ip address from horizon logs into ELK stack.

# 11/18/2016
Works I did today:

1. Set up ceilometer in the staging and get it running.

2. Run rally scenario test for ceilometer on and off.

Works to do for next week:

1. Research configure ssl for ceilometer for production use.

2. Set up elastic cluster in the staging to practice extend node.

3. Get ip address from horizon logs into ELK stack.

# 11/17/2016
Works I did today:

1. Researched how to extend elastic cluster.

2. Installed and set up ceilometer in the staging. I will continue working on it later tonight and tomorrow.

Works to do for tomorrow:

1. Continue setting up ceilometer.

2. Run rally and tempest tests for ceilometer on and off.

3. Set up elastic cluster in the staging to practice extend node.

# 11/16/2016
Works I did today:

1. Run several tempest scenario tests with ceilometer on and off.

2. Debug OpenStack. There are issues with my staging OpenStack when creating new instances. It seems that the operating system could not be installed in the new instance. Have worked with Rado and Laura for several hours but still couldn’t find the reason. The staging environment is being rebuild now. And after that I will need to set up the ceilometer again.

Works to do for tomorrow:

1. Verify the staging.

2. Install and set up ceilometer.

3. Run tests.

# 11/15/2016
Works I did today:

1. Set up tempest in a vm.

2. Run tempest full test with ceilometer on in my staging and generate html report.

3. Run tempest scenario test with ceilometer on.

Works to do for tomorrow:

1. Run Tempest scenario tests with ceilometer off and generate report.

2. Research on improving ELK stack.

# 11/14/2016
Works I did today:

1. Debugged nova in my stagging environment. Solved the nova cannot create instance issue.

2. Run Rally test in my staging and write report.

3. Installed Tempest and run Tempest test.

Works to do for tomorrow:

1. Continue run Tempest tests and write report.

2. Research getting ip address from https logs.

# 11/11/2016
Works I did today:

Today I mainly debugged openstack in the stagging environment. Get ceilometer running. And tried to run Rally test. However, when run nova test I got error message "No valid host was found. There are not enough hosts available.” I tried several hours tried to solve this issue but not accomplish yet. I will continue looking into this bug.

Works to do for next week:

1. Run Rally test.

2. Set up and run Tempest test.

# 11/10/2016
Works I did today:

1. Debug ELK stack. Get elasticsearch running. Problem is elasticsearch default java heap size is 1GB which is too small. Set it to 4GB now it is running.

2. Rebuild my staging environment and set up ceilometer.

3. Learn tempest.

Works to do tomorrow:

1. Run Rally test.

2. Set up Tempest.

# 11/9/2016
Works I did today:

Today I tried to do rally test on my staging, setting up tempest and debuging openstack. When I tried to install tempest the openstack broken in my staging, probably because of multipule python packages installed. I have talked to Rado and he said he will rebuild the staging environment for me. After I got the staging rebuild I will start doing the test again.

Works to do tomorrow:

1. Run Rally test.

2. Set up Tempest.

3. Debug Elasticsearch.

# 11/8/2016
Works I did today:

Today I worked on installing and debugging tempest in my staging. Also I learned how to use python virtual environment. I installed tempest but it is not working yet. I will continue debugging it.

Works to do tomorrow:

1. Continue working on tempest test.

2. Get ip address from horizon logs into ELK stack.

3. Ceilometer get user resource usage data.

# 11/7/2016
Works I did today:

1. Read documents about ceilometer. Links I refered to are mainly: https://www.rdoproject.org/install/ceilometerquickstart/, http://docs.openstack.org/developer/ceilometer/architecture.html, https://access.redhat.com/documentation/en/red-hat-openstack-platform/9/paged/logging-monitoring-and-troubleshooting-guide/.

2. Debug and learn ELK stack.

Works to do next week:

1. Working on tempest test.
2. Get ip address from horizon logs into ELK stack.
3. Ceilometer get user resource usage data.

# 11/4/2016
Works I did today:

1. Today I debugged ELK stack. The issue is: we cannot get results from kibana (loading forever, page hangs up). There are errors in logstash and elasticsearch status is red also.

What I did to solve the issue are: Set elasticsearch 'number of replicas' to 0 since there is only 1 elasticsearch node; Set logstash input "congestion_threshold" to 40 instead of 5 (default). To let it waiting longer before raise a timeout. Changed kibana "elasticsearch.requestTimeout" from 30s to 300s. Let kibana waiting longer. Also delete duplicate kibana services in systemd deamon.

Now ELK stack is running. We could see logs in Kibana. But it is still very slow compared to 1 month ago. Need to continue looking into this issue.

2. Learned ELK stack toturials and documents.

Works to do next week:

1. Working on tempest test.

2. Get ip address from horizon logs into ELK stack.

3. Ceilometer get user resource usage data.

# 11/3/2016
Works I did today:

1. Today I mainly did Rally OpenStack performance test for ceilometer on and off. I run six test cases. They are: 1, nova test that boots and deletes multiple servers. 2. Nova create-and-get-flavor test 3. Keystone add and remove user role test 4. Keystone create, update and delete tenant test 5. Glance create and delete image test 6. Neutron create and delete networks test.

2. The production ELK stack is not working properly. Logstash got error messages "Beats input: the pipeline is blocked, temporary refusing new connection.” And also kibana is too slow. Need to continue looking into this bug.

Works to do tomorrow:

1. Working on tempest test.

2. Debug ELK stack.

#11/2/2016
Works I did today:

1. Practiced setting up Gnocchi database in the staging.

2. Debugged Kibana in moc-logstash server. Kibana will not responding(loading unti timeout) when searching for logs. Have installed newer versions of kibana and solved the problem for a while. Now after several big searches, the problem happen again. I will continue working on this.

3. Learned ceilometer components and functions.

Works to do tomorrow:

1. Continue researching resource usage data from ceilometer.

2. Debug Kibana.

2. Get ip address information from horizon logs and store them properly into elasticsearch and influxdb.

# 11/1/2016
Works I did today:

Today I mainly researched how to get resource usage data from ceilometer. We may could get the data from gnocchi database since gnocchi is used to store time series measurement data. And ceilometer api has been deprecated in the newer version of ceilometer. I will try to install and learn gnocchi database.

Works to do tomorrow:

1. Continue researching resource usage data from ceilometer. Install gnocchi database for metric storage.
2. Get ip address information from horizon logs and store them properly into elasticsearch and influxdb.

# 10/31/2016
Works I did today:

Today I set up filebeat in the ceph vm to collect ceph logs. Transported the logs into logstash and elasticsearch on moc-logstash server. And also stored the logs into influxDB ceph_logs measurement.

Works to do tomorrow:

1. Research resource usage data from ceilometer.

2. Get ip address information from horizon logs and store them properly into elasticsearch and influxdb.

# 10/21/2016
Works I did today:

1. Set up ceilometer in compute-207 in the new staging.

2. Set up Aodh in ceilometer controller node to enable alarming services.

3. Set up Rally to test the openstack services in the new staging.

4. Ran nova boot and delete test. Learned and practiced Rally.

# 10/20/2016
Works I did today:

1. Set up Rally in my staging.

2. Debugged production ELK stack. Make them running.

3. Get a new staging environment from Rado. Which contains two controller nodes and two compute nodes.

Works to do tomorrow:

1. Set up ceilometer and rally in the new staging.

3. Get resource usage data from ceilometer.

# 10/19/2016
Works I did today:

1. Debugged ceilometer meter service. Solved the command not responding bug. By modifying the endpoint information and restarting all ceilometer services.

2. Installed Aodh and setted up the ceilometer alarm service.

Works to do tomorrow:

1. Set up Time-Series-Database-as-a-Service (gnocchi) in my staging.

2. Practice ceilometer notification service.

# 10/18/2016
Works I did today:

1. Create one alarm in ceilometer that will create a log file when the cpu usage of  one instance is more 70%.

2. Installed Aodh in my staging for ceilometer alarming function.

3. Learned MongoDB.

Works to do tomorrow:

1. Continue setting up ceilometer alarm service.

2. Debug ceilometer.

# 10/17/2016
Works I did today:

1. Setup ceph in my Kaizen VM.

2. Collected ceph logs into ELK stack.

Works to do tomorrow:

Testing/setting up alarms and meters in Ceilometer.

# 10/14/2016
Works I did today:

1. Helped requesting a bug fix to get user information in logs. Wrote a google doc to describe the bug and analysis logs from different services. The link is: https://docs.google.com/document/d/1gHgSr3NH8ncIM_zHawnvcFMjsbYHhZkGDmSCoqS5aE0/edit?usp=sharing.

2. Read documents about ceph and tried to install ceph in my staging. Faced errors that could not create ceph monitor. Will continue working on it.

Works to do next week:

1. Set up ceph and ELK stack in my staging environment.
2. Collect ceph logs into ELK stack.

# 10/13/2016
Works I did today:

Today I got ceilometer running in my staging environment finally. I rebuild my staging environment again, both the controller and compute nodes, to get a new start. Then I followed the guide online and installed every parts of ceilometer. Solved the bugs and now it is running.

Works to do tomorrow:

1. Learn how to use ceilometer to monitor the staging environment.

2. Practicing collecting Ceph logs into ELK stack. 

# 10/12/2016
Works I did today:

Today I rebuild my staging environment for both the controller node and compute node. And then installed the ceilometer followed red hat ceilometer installation guide.

Works to do tomorrow:

Configure ceilometer.

# 10/11/2016
### Works I did today:

Today I continued debuging ceilometer and openstack service in my staging environment. The bug is: I cannot run any openstack CLI command, I got SSL error. By working with Laura and Chrissiti, we solved the bug in the controller node. The solution is to set the certification file path in the environment variable. But I still cannot run the openstack command in the compute node. Need to continue working on this.

### Works to do tomorrow:

1. Continue learning and configuring ceilometer. Learn how to use meters.

2. Debug openstack service in my compute node. 

# 10/07/2016
### Works I did today:

1. Today I continued configuring and debugging ceilometer in my staging. Solved several bugs in ceilometer. Now ceilometer in my staging is up and running.

2. Enabled image service meters, compute service meters, block storage service meters in ceilometer.

### Works to do next week:

1. Continue learning and configuring ceilometer. Learn how to use meters.

2. Analyze *_metrics data in Pig

# 10/06/2016
### Works I did today:

Install, configure and correct ceilometer.

Today I got more familiar with ceilometer services and configuration. It’s not working yet but I will try my best to deal with it. I corrected the bug that ceilometer endpoint didn’t direct to the right address. By recreating the ceilometer endpoint and reinstall ceilometer. I also corrected line by line the ceilometer configuration file. Now I didn't get the SSL error but the ceilometer still couldn’t connect to localhost (10.14.37.215) yet. I will continue working on it. 

### Works to do tomorrow:

Configuring and learning ceilometer in my staging.

# 10/05/2016
### Works I did today:

1. Learned Hadoop and pig.

2. Debug ceilometer.

3. Worked with Rado to reboot my staging using puppet. Deleted the old ceilometer endpoint. And builded a new ceilometer endpoint that points to my staging environment.

### Works to do tomorrow:

1. Since my staging has been rebooted. Need to install ceilometer.

2. Continue configuring and learning ceilometer in my staging.


# 10/04/2016
### Works I did today:

1. Finished configuring ceilometer in my staging according to the document. The service is running but could not open ceilometer api. Still trying to debug this.

2. The memory usage in the controller node of my staging is too high (more than 90%). Tried to solve this problem. 

One reason maybe that there are 25 nova-api processes running at the same time which occupied around 30% memory. By restarting the nova service and also neutron services, now the memory usage has dropped to 73.6%. Which is also high. 

Another reason maybe the ceilometer service that I just set up in my staging. Since the memory usage of rabbitmq is also high (2.5% memory). I have stopped all ceilometer service now. Need to looking more into this problem.

3. Learned Hadoop pig scripts and usage matrics data. In order to help doing Jeremy’s work in the future.

### Works to do tomorrow:

1. Learn pig.

2. Debug Ceilometer.

3. Research the memory usage problem.

# 10/03/2016
### Works I did today:

Today I practiced setting up ceilometer in my staging which is openstack Liberty release. I installed and configured MongoDB as the meter database. The following ceilometer service is running now in my staging: "openstack-ceilometer-alarm-evaluator.service, openstack-ceilometer-alarm-notifier.service, openstack-ceilometer-api.service, openstack-ceilometer-central.service, openstack-ceilometer-collector.service, openstack-ceilometer-notification.service”.

### Works to do tomorrow:

1. Continue configuring ceilometer.

2. Learn how to use ceilometer.

# 09/30/2016
Works I did today:

1. Modified the sensu handler to output compute_metrics to influxdb.

2. Learned and practiced openstack python api

3. Learned cloud computing basics

Works to do for next week:

1. Learn ceilometer and openstack mitaka.

2. Practice deploying ceilometer in my current staging environment.

# 09/29/2016
Works I did today:

1. Learned OpenStack command line api.

2. Worked with Jeremy and Vinay, tried to get vm - host mapping. Learned checks and handlers code. Modified Jeremy’s code to add instance_id and tenant_id to the check result.

Works to do tomorrow:

1. Continue modifying the checks and handler code. Try to add tenant name field.

2. Learn ceilometer.

# 09/28/2016
Works I did today:

Today I mainly learned ceilometer basics by watching presentations and reading documents online. I also learned cloud computing basics by watching online classes.

Works to do tomorrow:

1. Continue learn openstack mitaka and ceilometer.

2. Work with the ops team to deploy logstash output to influxdb.

# 09/27/2016
Works I did today:

1. Fixed logstash output issue. Let logstash output data to influxdb.

2. Learned filebeat puppet code.

3. Learned grafana.

Works to do tomorrow:

1. Working with ops team. Try to solve the horizon log generation problem.

2. Making horizon login attempts graphs in grafana.

# 09/26/2016
Works I did today:

1. Fixed the bug that could not start logstash in moc-logstash server.

2. Debugging logstash output influxdb.

Works to do tomorrow:

Continue fixing the logstash output issue. Try to make the new output running.

# 09/23/2016
Works I did today:

1. In my staging, added a new Logstash output to influxdb. So that whenever there is a new login attempts through horizon, there will be a new record in influxdb which containing login success/failed information, username and time.

2. Added the new logstash output in the production logstash server. But it is not working. Still debugging it now.

Works to do next week:

Continue fixing the logstash output issue. Try to make the new output plugin working.

# 09/22/2016
Works I did today:

1. Helped Trishita get acccess to nodes.

2. Learned how to set logstash to output to influxdb.

Works to do tomorrow:

1. Try to add user information in Sensu horizon check results.

# 09/21/2016
Works I did today:

1. Watched videos and documents about openstack basics, Ceilometer and Mitaka.

2. Modified sensu elastic checks to be excuted every 1 minute, and query the login activities for the past 1 minute. Also modified the outcome of grafana graphs to be consistent with the production.

Works to do tomorrow:

Practicing setting up Ceilometer in staging environment.

# 09/20/2016
Works I did today:

1. Experimented different operations in Openstack, like create an instance, delete an instance, log in/out..., tried to get different log message samples. But there are some errors in my staging Openstack. The rabbitmq-server is not working properly now. Trying to debug it.

2. Installed Grafana, visualized the new measurements that I build in influxdb in Grafana.

Works to do tomorrow:

Implement the new checks, handlers and grafana dashboards to the production.

# 09/19/2016
Works I did today:

1. Wrote sensu elastic checks and handlers in my staging. That will check every 30 minutes the number of login success and failed attempts in horizon and then record this number in tables in influxdb.

2. Wrote documents about different logs in openstack.

Works to do tomorrow:

1. Continue writing documents to explain different logs in openstack. 

# 09/16/2016
Works I did today:

1. Finished practicing Sensu checks and handlers to pull statistics from elastic and push into influxdb. Wrote a check and handler in my staging. That will check every 30 minutes the number of login attempts in horizon and then record this number in a table in influxdb.

2. Attended the Docker/Container workshop. Learned basic knowledges of Docker, container and OpenShift.

Works to do next week:

1. Write more Sensu checks and handlers to pull statistics from elastic and push into influxdb.

2. Write document to explain different logs in openstack.

# 09/15/2016
Works I did today:

1. Installed a elasticsearch check in my staging and tried to get it running.

2. Helped Trishita get access to node-39, moc-sensu server and kaizen-sensu-admin (Influxdb).

3. Helped Apoorve build Sensu system in engage-1.

4. Added my and Vinay’s emails to moc sensu mailing list.

Works to do tomorrow:

Practice in my staging environment. Write sensu checks and handlers to pull statistics from elastic and push into influxdb.

# 09/14/2016
Works I did today:

1. Practiced adding a influxdb handler and a check to push check results into influxdb.

2. Wrote a new influxdb handler for a check to learn how influxdb handler works.

Works to do tomorrow:

Write Sensu checks and handlers to pull statistics from elastic and push into influxdb.

# 09/13/2016
Works I did today:

1. Practiced sensu checks and handlers.

2. Practiced adding a remote sensu client in my staging.

3. Practiced sensu client server system.

Works to do tomorrow:

Write sensu checks and handlers to pull statistics from elastic and push into influxdb. 

# 09/12/2016
Works I did today:

1. Installed and practiced influxdb in my staging.

2. Installed and practiced sense in my staging.

Works to do tomorrow:

Write Sensu checks and handlers to pull statistics from elastic and push into influxdb.

# 09/09/2016
### Works I did today:

1. Configured Snapshot in my staging.

2. Learned/reviewed Sensu checks, handlers and Influxdb.

### Works to do for next week:

Write Sensu checks and handlers to pull statistics from elastic and push into Influxdb.

# 09/08/2016
### Works I did today:

1. Configured snapshot. Still cannot make it running now. I will continue looking into it and also see whether there is other way to realize this functionality.

2. Learned Docker.

3. Changed horizon time zone from UDT to EDT in my staging environment and puppetized it into production. This is because horizon time zone is in UDT which is inconsistant with any other services (in EDT) in Openstack. The change will let the log time display in Kibana clearer.

### Works to do tomorrow:

1. Investigating ELK Stack report schema in my staging.

2. Looking into configuration files and python codes in OpenStack services. Try to get user information in logs.

# 09/07/2016
### Works I did today:

1. Created new alerts onto ElastAlert in production ELK. The first alert is: Alert when 10 horizon login failures occur for a single user. Another alert is: Alert if number of errors triples in an hour.

2. Investaged how to generate reports in Kibana. Learned snapshot.

### Works to do tomorrow:

1. Continue configuring snapshot. Make it running. 

# 09/06/2016
### Works I did today:

1. Today I investigated how to generate reports in Elasticsearch. There are several commercial softwares that could realize this functionality but they are not free to use. There is a free one called snapshot. I have installed it in my staging and I am configuring it now.

2. Investigating what kind of alerts we need for ElasticSearch.

### Works to do tomorrow:

1. Add more alerts onto ElastAlert.

2. Configuring snapshot. Make it running.

# 09/02/2016
### Works I did today:

Today I installed Elastalert in production logstash server and configured it. I have create one alert in my staging. The alert is that when there are certain number of failed logins happen in horizon in a period of time I will got an email notification. However the same configuration didn’t work in the production. I am debugging it now.

### Works to do for next week:

1. Continue debugging Elastalert. Make the alert running.
2. Add more alerts on it.
3. Investigate how to make kibana send reports periodicly. 

# 09/01/2016
### Works I did today:

1. Added a new filter to production Logstash. Now it could parse username in horizon logs. Let it possible to track user activity through horizon.

2. Created a new dashboard called ‘horizon login activities’ in production Kibana.  We could see login success/failed count, user name through time. 

### Works to do tomorrow:

Configure ElasticAlert, send notifications when abnormal things happen. 

# 08/31/2016
### Works I did today:

1. Modified filter in Logstash. Let the filter grab user id information in nova api logs. It is userful for us to visualize user activity in Kibana.

2. Created new graph to show user activity in nova-api logs.

### Works to do tomorrow:

1. Add new filter or function to Logstash. Let it to grab username information in horizon logs.
2. Configure ElasticAlert.

# 08/30/2016
### Works I did today:

1. Configured ElastAlert. Build a rule called 'Horizon failed login alert'. It will check periodically the horizon 'login failed' messages. If 'login failed' happens more than 5 times in the period(1 hour) then an email alert will be sent to me. This rule could be useful in detecting attacks.

2. Create a graph in Kibana that will show number of nova api requests with time by different loglevels. 

### Works to do tomorrow:

1. Continue configuring ElastAlert. Add more rules and alerts to it.
2. Investigate how to make kibana send reports periodicly.
3. Continue thinking about how to grab user information in keystone logs.
4. Make more meaningful graphs in Kibana.

# 08/29/2016
### Works I did today:

1. Today I investigated ways to provide alerting and notification based on logs collected in Elasticsearch, ELK Stack. Elastic has its own software called ‘Watcher’ to realize this function. However, it is a commercial software and not free. ElastAlert maybe a good choice. It is a framework for alerting on anomalies, spikes, or other patterns of interest from data in Elasticsearch. It is an opensource software developed by Yelp.

2. Downloaded ElastAlert in my staging and configured it.

### Works to do tomorrow:

1. Continue configuring ElastAlert. Add some rules and alerts to it. Make it running.

2. Investigate how to make kibana send reports periodicly.

3. Continue thinking about how to grab user information in keystone logs.

# 08/26/2016
### Works I did today:

Today I did some experiment in keystone and nova in my staging. We could get user id in nova logs but there seems to be a bug in keystone to realize the same function. Once we got the user id, we could run ‘openstack user show <user id>’ in command line to get this user information.

Laura and Kristi helped me a lot on figuring out the problem. Laura said she would file a bug report on this to openstack team. In newer version of openstack, mitaka, the code of generating log files is different than the liberty version. Maybe we could get the right result in the newer version.

### Works to do next week:

1. Continue thinking about how to fix this problem and get more user information.

2. Hopefully I could get some new tasks and make more contributions to the team.

# 08/25/2016
### Works I did today:

Today I looked into keystone configuration files and openstack keystone code. There is a ‘Logging_context_format_string’ variable in keystone.conf file that defines the format and content of the logs. Below is the value:

logging_context_format_string = %(asctime)s.%(msecs)03d %(process)d %(levelname)s %(name)s [%(request_id)s %(user_identity)s] %(instance)s%(message)s

This format should enable printing user identities in the logs but it doesn’t work. For an example, when I log in with a wrong password, in keystone I only got log message like “2016-08-22 17:00:15.396 2707 WARNING keystone.common.wsgi [req-c9ab9248-4f94-4ed0-9005-10fe3c5e5486 - - - - -] Authorization failed. The request you have made requires authentication. from 10.14.37.215”. We think the user id should be next to the request id but we only got ‘-‘.

This may be a bug in keystone. I asked questions in keystone IRC channel and am waiting for response. I got help from Laura and Kristi, they will also look into this problem in the mean time.

### Works to do tomorrow:

1. Do experiment on keystone configuration file in staging. See whether we could get user id by just changing the config.

2. Digging into openstack keystone code. Finding the part that defines how to generate logs.

3. Modify the openstack keystone code.

# 08/24/2016
### What I learned today

Today I studied MOC monitoring wiki page and OpenStack keystone code repository. I get familiar with influxDB, sensu, ceilometer and grafana. I also found that in order to get user information in keystone logs we may need to change the keystone OpenStack code.

### Works to do tomorrow

Continue learning OpenStack.

# 08/23/2016
### What I learned today

Today I learned Logging facility for Python. Because the logging configuration of openstack services are written by python. The link is: https://docs.python.org/3/library/logging.html#logging.Formatter

I learned that we could change the log format by modifying the configuration file. But I still not sure how to add user information on this. I will keep working on it.

### Works to do tomorrow

* Continuing learning logs in Openstack.

# 08/22/2016
Works I did today:

Today I did an experiment to try to log in to OpenStack and catch the log messages of the actions. My ip address is: 129.10.5.39. In the horizon dashboard of my staging environment. Firstly I tried to log in with a wrong password. I got the following log messages in keystone.

`2016-08-22 17:00:15.263 2707 INFO keystone.common.wsgi [req-c9ab9248-4f94-4ed0-9005-10fe3c5e5486 - - - - -] POST https://controller-215-ruoyu.staging.moc.edu:5000/v2.0/tokens`
`2016-08-22 17:00:15.396 2707 WARNING keystone.common.wsgi [req-c9ab9248-4f94-4ed0-9005-10fe3c5e5486 - - - - -] Authorization failed. The request you have made requires authentication. from 10.14.37.215`
`2016-08-22 17:00:15.398 2707 INFO eventlet.wsgi.server [req-c9ab9248-4f94-4ed0-9005-10fe3c5e5486 - - - - -] 10.14.37.215 - - [22/Aug/2016 17:00:15] "POST /v2.0/tokens HTTP/1.1" 401 427 0.144679`

The 10.14.37.215 is the openstack server node address in my staging environment. There is time here. The information within the brackets are: [%(request_id)s %(user_identity)s].

If I log in with the right user name and password the corresponding logs in keystone will be:
`2016-08-22 17:20:03.745 2701 INFO keystone.common.wsgi [req-a693f18f-7f0d-456f-b52c-578a8fe48362 - - - - -] POST https://controller-215-ruoyu.staging.moc.edu:`
`5000/v2.0/tokens`
`2016-08-22 17:20:03.923 2701 INFO eventlet.wsgi.server [req-a693f18f-7f0d-456f-b52c-578a8fe48362 - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:03] "POST /v2.`
`0/tokens HTTP/1.1" 200 589 0.189524`
`2016-08-22 17:20:03.928 2701 INFO keystone.common.wsgi [req-a693f18f-7f0d-456f-b52c-578a8fe48362 - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:5`
`000/`
`2016-08-22 17:20:03.928 2701 INFO eventlet.wsgi.server [req-a693f18f-7f0d-456f-b52c-578a8fe48362 - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:03] "GET / HTT`
`P/1.1" 300 810 0.001482`
`2016-08-22 17:20:03.940 2701 INFO keystone.common.wsgi [req-26dfb844-669c-4994-9ca7-fb437ef5074d - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:5`
`000/v2.0/tenants`
`2016-08-22 17:20:03.953 2701 INFO eventlet.wsgi.server [req-26dfb844-669c-4994-9ca7-fb437ef5074d - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:03] "GET /v2.0`
`/tenants HTTP/1.1" 200 362 0.022076`
`2016-08-22 17:20:03.970 2701 INFO keystone.common.wsgi [req-ce884b5d-9f16-4bc8-bd8f-0e35c8dd805f - - - - -] POST https://controller-215-ruoyu.staging.moc.edu:`
`5000/v2.0/tokens`
`2016-08-22 17:20:04.038 2701 INFO eventlet.wsgi.server [req-ce884b5d-9f16-4bc8-bd8f-0e35c8dd805f - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:04] "POST /v2.`
`0/tokens HTTP/1.1" 200 5393 0.074301`
`2016-08-22 17:20:04.327 2659 INFO keystone.common.wsgi [-] GET https://controller-215-ruoyu.staging.moc.edu:35357/`
`2016-08-22 17:20:04.331 2659 INFO eventlet.wsgi.server [-] 10.14.37.215 - - [22/Aug/2016 17:20:04] "GET / HTTP/1.1" 300 812 0.005760`
`2016-08-22 17:20:04.357 2659 INFO keystone.common.wsgi [req-dac2fdea-e204-4515-8518-c9b54567ef41 - - - - -] POST https://controller-215-ruoyu.staging.moc.edu:`
`35357/v2.0/tokens`
`2016-08-22 17:20:04.570 2659 INFO eventlet.wsgi.server [req-dac2fdea-e204-4515-8518-c9b54567ef41 - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:04] "POST /v2.`
`0/tokens HTTP/1.1" 200 5389 0.234656`
`2016-08-22 17:20:04.650 2659 INFO keystone.common.wsgi [req-292a0b58-78b4-4ea9-9ef0-0a1f9c0a6ab5 - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:3`
`5357/v3/auth/tokens`
`2016-08-22 17:20:04.703 2659 INFO eventlet.wsgi.server [req-292a0b58-78b4-4ea9-9ef0-0a1f9c0a6ab5 - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:04] "GET /v3/a`
`uth/tokens HTTP/1.1" 200 9040 0.130179`
`2016-08-22 17:20:05.439 2680 INFO keystone.common.wsgi [req-8928c721-ce24-46ab-b56f-ea0c14db7396 - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:3`
`5357/`
`2016-08-22 17:20:05.440 2680 INFO eventlet.wsgi.server [req-8928c721-ce24-46ab-b56f-ea0c14db7396 - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:05] "GET / HTT`
`P/1.1" 300 812 0.001881`
`2016-08-22 17:20:05.446 2680 INFO keystone.common.wsgi [req-1b009fe5-f338-4390-a47e-417688be0b4a - - - - -] POST https://controller-215-ruoyu.staging.moc.edu:`
`35357/v2.0/tokens`
`2016-08-22 17:20:05.518 2680 INFO eventlet.wsgi.server [req-1b009fe5-f338-4390-a47e-417688be0b4a - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:05] "POST /v2.`
`0/tokens HTTP/1.1" 200 5389 0.074102`
`2016-08-22 17:20:05.529 2680 INFO keystone.common.wsgi [req-7d805147-da62-456a-a4f6-b94f878ae087 - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:35357/v3/auth/tokens`
`2016-08-22 17:20:05.572 2680 INFO eventlet.wsgi.server [req-7d805147-da62-456a-a4f6-b94f878ae087 - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:05] "GET /v3/auth/tokens HTTP/1.1" 200 9040 0.050966`
`2016-08-22 17:20:06.539 2680 INFO keystone.common.wsgi [req-d3e4c03d-2160-4e62-b804-5fa34e2830c1 - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:35357/v3/auth/tokens`
`2016-08-22 17:20:06.636 2680 INFO eventlet.wsgi.server [req-d3e4c03d-2160-4e62-b804-5fa34e2830c1 - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:06] "GET /v3/auth/tokens HTTP/1.1" 200 9040 0.106221`
`2016-08-22 17:20:07.053 2680 INFO keystone.common.wsgi [req-ce6f24ec-cfde-4038-a67c-eeb8e8a0905e - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:35357/v3/auth/tokens`
`2016-08-22 17:20:07.097 2680 INFO eventlet.wsgi.server [req-ce6f24ec-cfde-4038-a67c-eeb8e8a0905e - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:07] "GET /v3/auth/tokens HTTP/1.1" 200 9040 0.051758`
`2016-08-22 17:20:07.267 2680 INFO keystone.common.wsgi [req-ce6f24ec-cfde-4038-a67c-eeb8e8a0905e - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:35357/`
`2016-08-22 17:20:07.269 2680 INFO eventlet.wsgi.server [req-ce6f24ec-cfde-4038-a67c-eeb8e8a0905e - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:07] "GET / HTTP/1.1" 300 812 0.002289`
`2016-08-22 17:20:07.274 2680 INFO keystone.common.wsgi [req-c165e968-c25a-4a27-b33e-3677e230e9f6 - - - - -] POST https://controller-215-ruoyu.staging.moc.edu:35357/v2.0/tokens`
`2016-08-22 17:20:07.347 2680 INFO eventlet.wsgi.server [req-c165e968-c25a-4a27-b33e-3677e230e9f6 - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:07] "POST /v2.0/tokens HTTP/1.1" 200 5395 0.075159`
`2016-08-22 17:20:07.357 2680 INFO keystone.common.wsgi [req-17a8b2cc-6de4-4c94-9624-b3526f11a62c - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:35357/v3/auth/tokens`
`2016-08-22 17:20:07.399 2680 INFO eventlet.wsgi.server [req-17a8b2cc-6de4-4c94-9624-b3526f11a62c - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:07] "GET /v3/auth/tokens HTTP/1.1" 200 9040 0.049391`
`2016-08-22 17:20:07.778 2662 INFO keystone.common.wsgi [req-b9f4c044-3aa5-4a40-8a2d-c7e3f48a1e9c - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:35357/v3/auth/tokens`
`2016-08-22 17:20:07.874 2662 INFO eventlet.wsgi.server [req-b9f4c044-3aa5-4a40-8a2d-c7e3f48a1e9c - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:07] "GET /v3/auth/tokens HTTP/1.1" 200 9040 0.107983`
`2016-08-22 17:20:07.987 2669 INFO keystone.common.wsgi [req-1d2f0dd2-4f20-45ad-a6ce-04be0dbea84b - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:35357/`
`2016-08-22 17:20:07.988 2669 INFO eventlet.wsgi.server [req-1d2f0dd2-4f20-45ad-a6ce-04be0dbea84b - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:07] "GET / HTTP/1.1" 300 812 0.001841`
`2016-08-22 17:20:07.994 2669 INFO keystone.common.wsgi [req-404d384f-d6b6-4344-92d5-08ba7ef4bdf5 - - - - -] POST https://controller-215-ruoyu.staging.moc.edu:35357/v2.0/tokens`
`2016-08-22 17:20:08.068 2669 INFO eventlet.wsgi.server [req-404d384f-d6b6-4344-92d5-08ba7ef4bdf5 - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:08] "POST /v2.0/tokens HTTP/1.1" 200 5393 0.074801`
`2016-08-22 17:20:08.079 2669 INFO keystone.common.wsgi [req-1fd5b3af-ce99-45f5-b3e4-7284db9d5d43 - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:35357/v3/auth/tokens`
`2016-08-22 17:20:08.180 2669 INFO eventlet.wsgi.server [req-1fd5b3af-ce99-45f5-b3e4-7284db9d5d43 - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:08] "GET /v3/auth/tokens HTTP/1.1" 200 9040 0.109540`
`2016-08-22 17:20:08.682 2667 INFO keystone.common.wsgi [req-3ae40d99-2ead-441b-af79-d6c55551dfcf - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:35357/v2.0/tenants`
`2016-08-22 17:20:08.689 2667 INFO eventlet.wsgi.server [req-3ae40d99-2ead-441b-af79-d6c55551dfcf - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:08] "GET /v2.0/tenants HTTP/1.1" 200 495 0.014280`
`2016-08-22 17:20:08.829 2704 INFO keystone.common.wsgi [req-51f6b1a3-5928-4210-af64-60303fbf175f - - - - -] GET https://controller-215-ruoyu.staging.moc.edu:5000/v2.0/tenants`
`2016-08-22 17:20:08.843 2704 INFO eventlet.wsgi.server [req-51f6b1a3-5928-4210-af64-60303fbf175f - - - - -] 10.14.37.215 - - [22/Aug/2016 17:20:08] "GET /v2.0/tenants HTTP/1.1" 200 362 0.099623`

We get a lot of “GET”, “POST” log messages for a single successful login.

### Works to do tomorrow:

* Continue studying keystone logs. Especially the request ID part.

# 08/19/2016
### Works I did today:

* Learned about OpenStack SystemUsageData. The link is: https://wiki.openstack.org/wiki/SystemUsageData.
* In my staging environment I tried to installed several debugging/monitoring utility for OpenStack. Such as StackTach (https://github.com/openstack/stacktach), and Yagi (https://github.com/rackerlabs/yagi).
* Read several documents about ceilometer. The links are: http://docs.openstack.org/developer/ceilometer/, http://docs.openstack.org/developer/ceilometer/overview.html, https://www.youtube.com/user/OpenStackFoundation

### Works to do next week:
Continue learning OpenStack. Trying to find cloud user activity information in OpenStack services.

# 08/18/2016
### Works I did today:

Today I was learning how to get different information in Openstack. The information include time, username, user ip, user browser, user machine, location etc. 

Currently these user info is not shown in any Openstack nova or keystone logs. I was trying to find whether we could modify log message content or could find these info in other place. I am also trying to find OpenStack notifications data. 

### Works to do tomorrow:

Continue learning openstack.

# 08/17/2016
### Works that I did today:

* Today I made a dashboard in production Kibana. The dashboard contains 5 graphs including ‘Login success count over time’, ‘Login failed count over time’, ‘Instance create count over time’, ‘Instance delete count over time’ and 'Keystone failed count'. The link of the dashboard is: http://10.13.37.99:5601/app/kibana#/dashboard/Openstack?_a=(filters:!(),options:(darkTheme:!f),panels:!((col:1,id:Login-success-count,panelIndex:1,row:1,size_x:6,size_y:3,type:visualization),(col:7,id:Login-failed-count,panelIndex:2,row:1,size_x:6,size_y:3,type:visualization),(col:1,id:Keystone-failed-count,panelIndex:3,row:7,size_x:6,size_y:3,type:visualization),(col:7,id:Instance-delete-count,panelIndex:4,row:4,size_x:6,size_y:3,type:visualization),(col:1,id:Instance-create-count,panelIndex:5,row:4,size_x:6,size_y:3,type:visualization)),query:(query_string:(analyze_wildcard:!t,query:%27*%27)),title:Openstack,uiState:(P-1:(vis:(legendOpen:!f))))&_g=(refreshInterval:(display:%2730%20seconds%27,pause:!f,section:1,value:30000),time:(from:now%2Fw,mode:quick,to:now%2Fw))
* Read some documents about monitoring openstack.

### Works to do tomorrow:
Continue learning openstack and monitoring tools.

# 08/16/2016
Works I did today:

* Read documents about openstack and log files. 

I found that we could get VM creation and deletion events information in nova logs. 

Every time we successfully create a new instance. There will be a log message in nova says 'Instance spawned successfully'. Thus, we could put these words in our kibana filter to get VM creation events over time.
The example log message is as follows:

2016-08-16 12:06:38.219 2937 INFO nova.virt.libvirt.driver [-] [instance: 273863eb-2569-4fd2-93bd-c0af732570c7] Instance spawned successfully.

Each time we delete a instance.
The nova log message is as follows:

2016-08-16 13:29:25.525 2937 INFO nova.virt.libvirt.driver [req-42de551d-bb4c-4cbd-ad7e-c965cca19dfd 71a0408904b1463083094115bad34608 07b813c3b1f24cae8f85b108ff05ce4a - - -] [instance: 4a6095b6-06ff-423e-a9dc-225823f40bf6] Deletion of /var/lib/nova/instances/4a6095b6-06ff-423e-a9dc-225823f40bf6_del complete

There are 'delete' and 'complete' keywords in the log file. We could filter the log message base on this.

I still need to figure out how to get 'number of active users in the system over time’ information from openstack log files.

* Add the elkstack code in github mocmon repository.

Works to do tomorrow:

* Make the plots in production Kibana.

# 08/15/2016

Works that I did today:

Today I read through different service's log files and docs in openstack. Trying to find useful information there. I need to learn about how to get usage information in openstack.

Here is what I found today:
* We could get number of active VMs information by running: 'nova list --all-tenants' command. I didn’t found such information in the openstack logs yet.

* We could get VM delete information in horizon logs. But maybe only if the VM was terminated through horizon dashboard. The example log is showing below:
2016-08-15 15:04:56,018 29025 INFO horizon.tables.actions Scheduled termination of Instance: “test_vm"

* Maybe we could write a check script. The check will periodicly run the command to check how many active VMs at that time and compare with the previous check. Thus we could get num. of VM creation and deletion over time.

Works to do tomorrow:

* Learn openstack. Learn about how to get usage information in openstack.

# 08/12/2016

* Finished writing and testing the new configurations of ELK stack in my staging environment. Made a pull request to kilo-puppet. The changes are waiting to be approved and deployed to the production environment.

The new configuration will let the logs be parsed to JSON format and also add tags to them. Making it easier when searching different logs in Kibana.

* Made three graphs in Kibana in my staging environment. The graphs I made are: number of failed authentications over time, number of successful login over time, and number of failed login over time.

User login information could be found in horizon logs. Below are the examples:

2016-08-12 18:25:10,334 4611 INFO openstack_auth.forms Login successful for user "admin".

2016-08-12 18:23:38,934 4611 INFO openstack_auth.views Logging out user "admin".

2016-08-12 18:12:26,885 4612 WARNING openstack_auth.forms Login failed for user "admin111".

'authorization failed' information comes from Keystone logs 'WARNING' level. Following is an example:

2016-08-12 14:12:26.883 2913 WARNING keystone.common.wsgi [req-550df77c-1351-4cbd-9b40-2dd67a5f934a - - - - -] Authorization failed. The request you have made requires authentication. from 10.14.37.215

### Works to do next week:

* Making graphs that we interested in the production Kibana.

Once we get the new changes deployed in the production environment. We should be able to see all logs nicely through Kibana. Next step is to grab the data we need in the logs we collected. And then making different real-time graphs to monitor the system.

# 08/11/2016

1. Modified ELK stack configuration in my staging environment.

Currently ELK stack is running normally on all nodes in production environment. We could collect all logs needed from all nodes, store them and visualize them. The problems are that logs from all compute nodes are not formatted. Besides, logs from controller nodes are formatted but not very well.

The targets are firstly parsing the log messages. Log messages from different services in openstack have similar format. For an example, below is a sample log message from nova service:

2016-08-08 17:59:44.150 5206 WARNING nova.compute.manager [req-b9d09438-40d8-4864-986e-f517a7a9cfe6 - - - - -] While synchronizing instance power states, found 8 instances in the database and 9 instances on the hypervisor.

The log message has the following format:
Date +  processID + Log level + module + request + logmessage

By writing regular expressions in filter and add the filter to logstash, we could parse the logs it collected, parse the log messages to nice JSON format. It will be helpful to our search and visualization in Kibana.

Secondly,  adding different ‘type’ label to different logs. It will also make our search and visualization in Kibana much more easier. For an example, all logs collected from /var/log/nova/ will be added “type:nova”.

### Works to do tomorrow:

* Finish testing new configurations of Filebeat and Logstash and Puppetize it.

I will try my best to finish writing and testing the new configuration in my staging environment. If I could get the right result. I will make a new pull request and Puppetize the changes in the production environment.

* Learning how to make different graphs in Kibana.

I will look into different logs in OpenStack. Trying to find useful information that we need.
