# Restore oVirt/RHEV VMs and their databases from backup (if any)

This document describes how to restore the VMs running on the oVirt setup.

VMs are backed up everyday with a week retention period.

## VMs

1. Gather the UUID of the instance's disk and the storage domain you are trying to restore from backup.

2. Logon to one of the oVirt/RHEV hosts.

Go to `/mnt/cephfs/ovirt/vmsbackup/`

```
[root@ov1 ~]# ls /mnt/cephfs/ovirt/vmsbackup/
Friday/    Monday/    Saturday/  Sunday/    Thursday/  Tuesday/   Wednesday/
```

Get the path to the backup from the day you think the VM was running.

3. `df -h` to find the mountpoint for the storage domain.


```
192.168.255.250:/mnt/drbd0                                     3.6T  1.1T  2.4T  32% /rhev/data-center/mnt/192.168.255.250:_mnt_drbd0
ov1.massopen.cloud:/data                                       7.9T  1.3T  6.7T  16% /rhev/data-center/mnt/glusterSD/ov1.massopen.cloud:_data
```

4. Overwrite the disk for the instance from the backup.

`pigz -dc < /mnt/cephfs/path/to/backup > /storage-domain-mntpoint/uuid`

`chown vdsm:kvm` of the disk otherwise it oVirt/RHEV can't access it.


## Database

The databases are backed up at `/dbback`  on the instance itself (e.g., both zabbix instances)

The idea is to delete the existing bad database, reinitialize a blank database, and then execute the sql statements from backup to recreate the database.

In case of mysql databases,
1. `rm -rf /var/lib/mysql/*` and restart database.
2. `bzip -dc < /dbback |mysql`

For postgresql,
1. Remove all files except the conf file from `/var/lib/pgsql/`
2. Restart and init the database (some postgres commands)
3. Recreate from backup (Lookup instructions for postgres).
