# MGHPCC Shutdown

## Before getting started

1. Double check with Norhteastern and BU if they are planning any network upgrades around the shutdown period.

## Turning off Kumo

1. Turn off all dell blades from the chassis management controller (easy).
2. Next step, turn off all VMs on Kumo Storage.
3. Turn off Kumo storage itself
4. Turn off Ceph
	Logon to kumoservices and then turn off kumo-ceph0{1-3}
5. Turn off VMs on kumo services (at least kumo-cephmanager is running on it)
6. Logon to kumo emergency (kumo-e.massopen.cloud) and turn off all the VMs there
6. Let kumo services and emergency box be on.

## Turning off Kaizen

1. Power off all elastic hosts (send it ipmitool chassis power soft)
2. Shutdown HIL, BMIm and vms on the kznbmihost1,2
3. Power off all the openstack VMs (in both Kaizens; we probably won't have old kaizen next year)
4. Then power off the computes, controllers.

## Turning off Engage1

1. Power off openstack VMs, then computes and then controllers.
2. Power off Ceph.
3. Power off VMs on engage1-services and emergency.

## oVirt VMs

1. Simply power off the VMs.
