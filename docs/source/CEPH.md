# CEPH issues

1. Receiving error message "something went wrong" when trying to access the object storage (Swift) pane of Horizon. Need solution. 

[pns:] What logs had we looked at so far ? 

2. Is it possible to share a single CEPH between two OpenStack environments ?  

3. Need Rados setup instruction

[pns:] Have we tried instruction at http://ceph.com/docs/cuttlefish/radosgw/manual-install/  ?

# CEPH game plan

[2015/01/29] Result of discussion among team @ Team room
To unblock project progress, all to focus on getting the 2nd environment being setup by Jon to function similar to the production environment. In parallel, contact to get redhat engaged in trouble shooting production with the CEPH issues listed above. 

**Second Environment:** (Jon/Ian/Huy)
More details can be found at [harvard dev env progress](https://github.com/CCI-MOC/moc/wiki/harvard-dev-env-progress) 

**Production**: 
**Redhat engagement**
[01/30/2015] Piyanai sent mail out to Blent (bholden@redhat.com); Redhat/MOC-Openstack mailing list (rh-moc-openstack@redhat.com) after clarifying the issues with Ian and George.

**Status**
[01/30/2015]
Swift:  Keystone's service catalogue states that the Swift API endpoint is on the Openstack controller---but nothing is there, and in fact nothing should be there.  Because of this, the Identity pane doesn't load at all.  Todo:

- Set up radosgw to be a Swift API endpoint on moc-rgw
- Open port
- Enable apache on boot
- Set up Keystone catalogue to point there
- Put moc-rgw on the public network, check that the other ports on it are secured

Cinder:  Cinder is correctly configured to use the 'rbd' (ceph) backend.  Creating volumes works, but attaching them doesn't---there's some bug here that we don't understand.  Cinder does not require moc-rgw to work.  Todo:

- Debug the volume attachment problem

Glance:  Glance is also correctly configured to use the 'rbd' (ceph) backend.  This appears to work perfectly, and does not require moc-rgw.

## Overview of Ceph debugging 1/30/2015 (Jason, George, Peter, Orran)

### The apache server on the radosgw machine was not running.
Tried starting it up using systemctl. For debugging, I enabled django's built-in stack tracing by setting DEBUG=True in `/etc/openstack-dashboard/local_settings`. Be sure to disable this for production by setting DEBUG=False.

### Keystone had bad swift endpoints, pointing to invalid URLs.
I suspect I still don't have the correct ones, but got these from the Ceph documentation at http://ceph.com/docs/master/radosgw/keystone/ . Note that the regionname defaults to lowercase, which causes more to fail.

This is how it was originally configured:

```
|                id                |   region  |                     publicurl                     |                  internalurl                   |                  adminurl                 |            service_id            |

| 756e8945c971464ca61e76aa1bd2400b | RegionOne | http://140.247.152.207:8080/v1/AUTH_%(tenant_id)s | http://10.31.27.207:8080/v1/AUTH_%(tenant_id)s |         http://10.31.27.207:8080/         | 9e55e68884a848deaab46182b448e343 |
| 9e55e68884a848deaab46182b448e343 |   swift    |   object-store  |  Openstack Object-Store Service  |

| 75f8d2353d764d6ba40143ef4513ac67 | RegionOne |            http://140.247.152.207:8386/           |           http://10.31.27.207:8386/            |         http://10.31.27.207:8386/         | 89e39e4304434254bc132d03a4ac2c48 |
| 89e39e4304434254bc132d03a4ac2c48 |   sahara   | data_processing |    Openstack Data Processing     |

| 8e1131b2983e4748aacd6fc5a3410604 | RegionOne |            http://140.247.152.207:8080            |            http://10.31.27.207:8080            |          http://10.31.27.207:8080         | 338b211240c443148bd310271c1d46b7 |
| 338b211240c443148bd310271c1d46b7 |  swift_s3  |        s3       |       Openstack S3 Service       |
```

This is how I reconfigured it:

```
http://blog.kri5.fr/2013/11/04/quick-radosgw-setup-for-existing-ceph-and-openstack-cluster/

keystone service-delete swift
# keystone service-delete swift_s3


keystone service-create --name swift --type object-store
keystone service-create --name swift_s3 --type s3

[root@controller openstack-dashboard(openstack_admin)]# keystone service-list
+----------------------------------+------------+-----------------+----------------------------------+
|                id                |    name    |       type      |           description            |
+----------------------------------+------------+-----------------+----------------------------------+
| 9e128b6926b048a986acb6d4b09071c3 | ceilometer |     metering    |    Openstack Metering Service    |
| 8890ae070ac6454abbe83e25c31686a2 |   cinder   |      volume     |          Cinder Service          |
| ff2dc15e3bac41eda0312897edd909ee |  cinderv2  |     volumev2    |        Cinder Service v2         |
| f4631c78ced94893ab5f28ffd179faee |   glance   |      image      |     Openstack Image Service      |
| 1af4340f6e13413ca2a37cadb1ebc98b |    heat    |  orchestration  | Openstack Orchestration Service  |
| 9abb55fb157843c2b6941635132cd8ac |  heat-cfn  |  cloudformation | Openstack Cloudformation Service |
| d2ad3da3ff7545b3a94fdd9144ac952a |  keystone  |     identity    |    OpenStack Identity Service    |
| 319579f6d2ba45279158fafff23beeb2 |  neutron   |     network     |    Neutron Networking Service    |
| d7b352e546f84aba874e286acc656756 |    nova    |     compute     |    Openstack Compute Service     |
| 93f50d038efb474dabca4e7a9a1be9df |  nova_ec2  |       ec2       |           EC2 Service            |
| 89e39e4304434254bc132d03a4ac2c48 |   sahara   | data_processing |    Openstack Data Processing     |
| eaa7711511a446c19e7dcd581da8b74c |   swift    |   object-store  |                                  |
| 9f7dcf1ac731415cbb79f14a5b6267c5 |  swift_s3  |        s3       |                                  |
+----------------------------------+------------+-----------------+----------------------------------+

[root@controller ~]# keystone endpoint-create --service-id eaa7711511a446c19e7dcd581da8b74c --publicurl http://10.31.27.223/swift/v1 --internalurl http://10.31.27.223/swift/v1 --adminurl http://10.31.27.223/swift/v1 --region RegionOne
+-------------+----------------------------------+
|   Property  |              Value               |
+-------------+----------------------------------+
|   adminurl  |   http://10.31.27.223/swift/v1   |
|      id     | 8ab9813848d14607a2b276089b214691 |
| internalurl |   http://10.31.27.223/swift/v1   |
|  publicurl  |   http://10.31.27.223/swift/v1   |
|    region   |            RegionOne             |
|  service_id | eaa7711511a446c19e7dcd581da8b74c |
+-------------+----------------------------------+
[root@controller ~]# keystone service-list
+----------------------------------+------------+-----------------+----------------------------------+
|                id                |    name    |       type      |           description            |
+----------------------------------+------------+-----------------+----------------------------------+
| 9e128b6926b048a986acb6d4b09071c3 | ceilometer |     metering    |    Openstack Metering Service    |
| 8890ae070ac6454abbe83e25c31686a2 |   cinder   |      volume     |          Cinder Service          |
| ff2dc15e3bac41eda0312897edd909ee |  cinderv2  |     volumev2    |        Cinder Service v2         |
| f4631c78ced94893ab5f28ffd179faee |   glance   |      image      |     Openstack Image Service      |
| 1af4340f6e13413ca2a37cadb1ebc98b |    heat    |  orchestration  | Openstack Orchestration Service  |
| 9abb55fb157843c2b6941635132cd8ac |  heat-cfn  |  cloudformation | Openstack Cloudformation Service |
| d2ad3da3ff7545b3a94fdd9144ac952a |  keystone  |     identity    |    OpenStack Identity Service    |
| 319579f6d2ba45279158fafff23beeb2 |  neutron   |     network     |    Neutron Networking Service    |
| d7b352e546f84aba874e286acc656756 |    nova    |     compute     |    Openstack Compute Service     |
| 93f50d038efb474dabca4e7a9a1be9df |  nova_ec2  |       ec2       |           EC2 Service            |
| 89e39e4304434254bc132d03a4ac2c48 |   sahara   | data_processing |    Openstack Data Processing     |
| eaa7711511a446c19e7dcd581da8b74c |   swift    |   object-store  |                                  |
| 9f7dcf1ac731415cbb79f14a5b6267c5 |  swift_s3  |        s3       |                                  |
+----------------------------------+------------+-----------------+----------------------------------+
[root@controller ~]# keystone endpoint-create --service-id 9f7dcf1ac731415cbb79f14a5b6267c5 --publicurl http://10.31.27.223/swift/v1 --internalurl http://10.31.27.223/swift/v1 --adminurl http://10.31.27.223/swift/v1 --region RegionOne
+-------------+----------------------------------+
|   Property  |              Value               |
+-------------+----------------------------------+
|   adminurl  |   http://10.31.27.223/swift/v1   |
|      id     | a85e878ae5da4b19905834b03aa93398 |
| internalurl |   http://10.31.27.223/swift/v1   |
|  publicurl  |   http://10.31.27.223/swift/v1   |
|    region   |            RegionOne             |
|  service_id | 9f7dcf1ac731415cbb79f14a5b6267c5 |
+-------------+----------------------------------+
``` 

For some unknown reason, horizon is continuing to obtain an unknown endpoint that has an invalid region.

```
 /usr/share/openstack-dashboard/openstack_dashboard/wsgi/../../openstack_dashboard/api/base.py in url_for

                                      endpoint_type)

            if not url and fallback_endpoint_type:

                url = get_url_for_service(service,

                                          region,

                                          fallback_endpoint_type)

            if url:

                return url

        raise exceptions.ServiceCatalogException(service_type)

    ...

    def is_service_enabled(request, service_type, service_name=None):

        service = get_service_from_catalog(request.user.service_catalog,

                                           service_type)

        if service:
```

This is the structure. The ID is invalid; something might be getting cached.

```
{u'endpoints': [{u'adminURL': u'http://10.31.27.223/swift/v1',
                 u'id': u'3f3bff86e3d9474a8526453c339c9993',
                 u'internalURL': u'http://10.31.27.223/swift/v1',
                 u'publicURL': u'http://10.31.27.223/swift/v1',
                 u'region': u'regionOne'}],
```

UPDATE: after a reboot, the problem above disapeared, and Horizon is now going to Ceph, and getting 
```
2015-01-30 22:34:08,241 9894 DEBUG openstack_dashboard.api.swift Swift connection created using token "e4983f45fb43dd23cccb38beec58adb9" and url "http://10.3
5:49 1.27.223/swift/v1"
5:49 2015-01-30 22:34:08,245 9894 INFO swiftclient REQ: curl -i http://10.31.27.223/swift/v1/a-container -X PUT -H "Content-Length: 0" -H "X-Auth-Token: e4983f45f
5:49 b43dd23cccb38beec58adb9" -H "x-container-read: "
5:49 2015-01-30 22:34:08,245 9894 INFO swiftclient RESP STATUS: 400 Bad Request
5:49 2015-01-30 22:34:08,245 9894 INFO swiftclient RESP HEADERS: [('date', 'Fri, 30 Jan 2015 22:32:45 GMT'), ('content-length', '226'), ('content-type', 'text/htm
5:49 l; charset=iso-8859-1'), ('connection', 'close'), ('server', 'Apache/2.4.6 (Red Hat) OpenSSL/1.0.1e-fips mod_fastcgi/mod_fastcgi-SNAP-0910052141')]
5:49 2015-01-30 22:34:08,245 9894 INFO swiftclient RESP BODY: <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
```

on the rados gateway we see:
```
5:51 tail -f ceph-radosgw.moc-rgw.log
5:51 2015-01-30 22:50:27.876790 7f91d17d2700  1 ====== starting new request req=0x7f920800d0e0 =====
5:51 2015-01-30 22:50:27.898980 7f91d17d2700  0 validated token: pjd-proj2:pjd expires: 1422660144
5:51 2015-01-30 22:50:27.900431 7f91d17d2700  1 ====== req done req=0x7f920800d0e0 http_status=404 ======
```

### Ceph has 2 bad disks.
This may or may not be affecting things.

```
[root@moc-ceph01-s ~]# ceph -s
    cluster 05c42710-8a31-448b-a785-5e89ed43cf22
     health HEALTH_WARN 836 pgs degraded; 1331 pgs stuck unclean; recovery 2855/26952 objects degraded (10.593%)
     monmap e2: 3 mons at {moc-ceph01-s=192.168.27.202:6789/0,moc-ceph02-s=192.168.27.203:6789/0,moc-ceph03-s=192.168.27.204:6789/0}, election epoch 156, quorum 0,1,2 moc-ceph01-s,moc-ceph02-s,moc-ceph03-s
     osdmap e188: 12 osds: 10 up, 10 in
      pgmap v472866: 4352 pgs, 13 pools, 48672 MB data, 8984 objects
            133 GB used, 9121 GB / 9255 GB avail
            2855/26952 objects degraded (10.593%)
                   3 active
                3021 active+clean
                 836 active+degraded
                 492 active+remapped
  client io 13205 B/s wr, 7 op/s
```


### Status of Cinder debugging (George)

- When creating a volume, it warns that node-12 doesn't exist,
  which is true and I'm not sure why Cinder cares.  Not sure if
  this is a problem

- When attaching a volume to a machine, the machine will list the
  volume as attached, but the volume won't list the machine as
  attached, it seems.  Further investigation needed.

- No obvious errors in Nova controller or Cinder controller logs.
  Need to investigate the logs of the relevant Cinder agent, and
  the Nova compute agent.