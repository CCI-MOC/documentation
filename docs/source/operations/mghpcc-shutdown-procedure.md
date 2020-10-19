# MGHPCC Shutdown

## Before getting started
1. Double check with Norhteastern and BU if they are planning any network upgrades around the shutdown period.

## Turn off Kumo
1. Turn off the VMs on dell blades. We have Ironic controllers and some ceph nodes.
1. Turn off all dell blades from the chassis management controller (easy).
1. Next step, turn off Kumo Storage (it's running FreeNAS)

## Turn off Kaizen
1. Power off all elastic hosts (send it ipmitool chassis power soft)
2. Shutdown HIL, BMIm and vms on the kznbmihost1,2.
3. Power off all the openstack VMs. This includes k-openshift.
4. Then power off the computes, and then the controllers.

For shutting down OpenStack (and bringing it back up), see this Red Hat
knowledge base article: https://access.redhat.com/solutions/1977013

A handy command line for stopping all running Nova instances:

```
openstack server list --status active --all-projects -f value -c ID |
  xargs -P10 -n1 openstack server stop
```

## Turn off the power9 cluster.
1. power it off,

## Turn off CNV and OCP cluster
1. Take an `etcd` backup
2. Shut down the the worker nodes
3. Shut down the controller nodes

For more information on shutting down and restarting OpenShift, see:

- https://docs.openshift.com/container-platform/4.5/backup_and_restore/graceful-cluster-shutdown.html
- https://docs.openshift.com/container-platform/4.5/backup_and_restore/graceful-cluster-restart.html#graceful-restart-cluster

## Turn off Engage1
1. Power off openstack VMs, then computes and then controllers.
2. Power off Ceph.
3. Power off VMs on engage1-services and emergency.

## Turn off MaaS VMs and Hosts
1. Turn off the VMs that are running the hil ipmi gateways.
1. Kumo Ceph and Kumo openstack are colocated on the 3 lenovo nodes, those nodes are provisioned under maas.

## The researd ceph cluster
1. Turn it off like how we turn off the production ceph.

## oVirt VMs
1. Simply power off all the VMs except for the hosted engine. Might want to turn off the ipmi-gw and dns server last.
1. Do not turn off the ovirt hosts. They will come online when power is restored.

## Power off the 2 SSD NFS servers
1. some oVirt VMs are using storage here, so we want to turn these off after the oVIrt VMs.
