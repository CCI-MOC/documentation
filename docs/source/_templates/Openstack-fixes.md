#### Neutron HA was failing in engage1
The variable `moc::cluster_deployment` was set to `!true` in the hiera file. Due to this, neutron HA was not configured. Fixed it and now neutron HA is working fine.

#### Unable to launch instances
Logs suggested that the ssl cert was not correct. Looked at logs in `/var/log/nova/` directory since launch of instances was failing on compute nodes and it was giving cert errors. Compute nodes were puppetized to have the correct certs.

#### Ceilometer taking too much CPU
Logs dir: `/var/log/ceilometer`

The error is because we moved the ceilometer database out from controller node to a separate node(mongodb server). This is ok but we need to cover up the issues which could arise from this. In case of network unreach, ceilometer-collector tries to reconnect to the database and it seems like its trying too aggressively. Some logs indicate that it is trying to make 10-20 or more connections per second. It is retrying from the past 7-8 days. I am not sure how much memory this might take, but it might be possible that rabbitmq message bus keeps on queuing up the messages until it is full and this results in high memory usage. There might be some parameters in ceilometer.conf file to fine-tune retries and message bus size, but someone needs to figure out and test this in staging environment before we enable it again in production.