# Monitoring

This document describes how we monitor our infrastructure and various services.

## Requirements

We wanted a system that could:

1. Monitor out infrastructure/services availibility.
2. Send notifications for alerts and triggers.
3. Gather metrics
4. Should be completely free

In the past we have been running Nagios Core to monitor availaiblity of various services using agentless checks like ping, http requests, and checking for services requests. For metrics collection, we used cacti with snmp agents on monitored hosts. This combination works fine, but leaves more to be desired. Nagios XI would be a significant upgrade but it is a paid product.

Various interns and coops at MOC had tried using other tools like sensu, prometheus but it didn't stick around.

So we decided to try Zabbix because:
1. It has enough features to replace Nagios Core and Cacti; on par with Nagios XI (paid product)
2. It's open source and completely free to use.
3. It's a classic monitoring system that has been around for a really long time (since 2001).

## Overview

All of our hosts used for monitoring are running as VMs on our oVirt/RHEV cluster.

For zabbix databases, we use nvme SSDs on HA-NFS (2 nodes).

1. Producition zabbix - `zabbix.massopen.cloud` for most of our monitoring.
2. Research Zabbix - `rz.massopen.cloud` (currently used for discovering and collecting metrics of our openstack VMs.)
3. ELK stack - `elk.massopen.cloud` (for log collections and analysis)
4. Cacti - `http://kzn-cacti.kzn.moc/cacti/` - still functional from our old setup. (graphing data)
5. nagios - `http://kzn-nagios.kzn.moc/nagios/` - still functional from our old setup. (alerts)
6. Grafana - For graphing zabbix data.
  - `status.massopen.cloud` for Production Zabbix (it's running on the same host as the zabbix server)
  - `rz.massopen.cloud/grafana` for Research Zabbix (no graphs here at the moment)

## Zabbix

This is our primary zabbix instance for monitoring pretty much everything.

### Configuration

32 GiB RAM and 16 vCPUs with 400 GB SSD Storage. A lot of it's configuration files and custom scripts live on [github](https://github.com/cci-moc/zabbix-config/)

It's running zabbix version 4.4. For database we are using postgres 11 with timescale DB extension.

### Zabbix agents

The primary way to monitor hosts and collect data is by using zabbix agents. We deploy zabbix agents on all our hosts using ansible. With Zabbix Agents we can get detailed metrics and alerts. Also, once zabbix agents are deployed the zabbix server can discover hosts on specified networks and apply a template with default metrics and triggers.

We also use custom parameters with zabbix agents. We add those to zabbix agent config files which can then invoke any arbitrary script with whatever parameters we like. [Config File](https://github.com/CCI-MOC/zabbix-config/blob/master/zabbix_agentd.conf)

### Notes about physical hosts

For physical hosts, we also monitor the ipmi controller.
- Ping the ipmi controller to check for availibilty.
- Most IPMI controllers also provide metrics and alerts over SNMP. This way we can get alerts in zabbix for drive failuers, RAID issues, various sensor informations etc.
- Baremetal hosts provided to users via HIL/BMI do not have zabbix agents, so we only monitor the ipmi controller.
- Baremetal hosts provided to users via MaaS have zabbix agents installed on them if they use our custom images. We let users know of this and they are free to remove the agents. We only collect metrics data, no triggers or alerts are generated in this case.

### Services

We monitor http, dns, dhcp, and some other services.

- For http services, the zabbix server calls an external script (that is on the same host) to check http response. We should switch to using the internal zabbix tools for this.
- We monitor dns and dhcp similarly too.

### Ceph

There's a zabbix ceph plugin that we use to get metrics abour ceph. However, it lacked some features that we desired:
1. Get ceph usage per root instead of per pool. So we wrote a [ceph root usage script](https://github.com/CCI-MOC/zabbix-ceph/tree/master/ceph_root_usage)
2. In addition to used space, we also like to know the provisioned space, so we wrote a [script to calculate that](https://github.com/CCI-MOC/zabbix-ceph/tree/master/ceph_provisioned)

### Inventory

Zabbix can store inventory data. We have written a [script to collect inventory data](https://github.com/CCI-MOC/zabbix-config/blob/master/scripts/inventory.sh) that we are interested in. The ansible playbook that deploys zabbix agents also deploys our script to gather inventory data.


### Alerts

Zabbix can push alerts to a variety of services. All alerts are configured to go the alerts mailing list which nobody besides Rado is subscribed to.

We also use a [script](https://github.com/CCI-MOC/zabbix-config/blob/master/alertscripts/rocketchat.py) to push zabbix alerts to rocket chat (and mirror the alerts channel in slack).

### Calculated metrics and graphs

We calculate aggregate usage of CPU, RAM, GPU accross various clusters (openstack computes, ovirt, power9). We also have custom graphs representing these.

## Research Zabbix

This is our research instance of zabbix. We do updates here first.

### Configuration

32 GiB RAM and 20 vCPUs with 300 GB SSD Storage. It's running the same version of zabbix and database as our primary zabbix.

### What does it do?

The server is collecting metrics about openstack instances. There are no zabbix agents used for this instance.

### zabbix-libvirt

Github Repo: `https://github.com/cci-moc/zabbix-libvirt`

These scripts use the libvirt API of our compute nodes to discover running VMs and then gather metrics about those instances. Once the metrics are gathered, it uses the zabbix api to create hosts corresponding to openstack instances in research zabbix (if it didn't already exist) and update the metrics. We use a cron job to run this every 2 minutes. The hosts are grouped into hosts groups corresponding to their project UUID and project name. Read [the documentation](https://github.com/CCI-MOC/zabbix-libvirt/blob/master/documentation.md) for more details

The host `openstack-monitoring.kzn.moc` runs these scripts every 2 minutes.

## Grafana

Grafana looks pretty and you can make nice dashboards with it. Both of our zabbix hosts have grafana installed on them too.

### For primary zabbix

This grafana dashboard is available at `status.massopen.cloud` and has very few graphs at the moment. No authentication is required to see the dashboards here.
The idea is to make this the status page of massopen.cloud (as the url suggest) so the front page will/should have information useful for public.

### For research zabbix

It's available at `rz.massopen.cloud/grafana`

This grafana dashboard exists, but we do nothing with it for now.

## ELK Stack

We are experimenting with elasticsearch for log collection and analysis.

## Nagios

From the old setup

## Cacti

From the old setup.
