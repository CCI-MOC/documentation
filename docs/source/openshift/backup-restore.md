## OpenShift Backup/Restore
Backup process is a series of cron jobs that back up the etcds, definition files to a remote system.

It is distributed between the nodes for HA.

On the etcds the following cron entries were made
```shell
        0 22 * * * etcdctl backup --data-dir /var/lib/etcd/ --backup-dir /admin/etcd.bak; cp /var/lib/etcd/member/snap/db /admin/etcd.bak/member/snap/db; tar -zcf /admin/e001.tgz /admin/ex.bak; rm -rf /admin/etcd.bak
```
On the masters collect all of the files from the nodes with the following cron entry for each node
```shell
        30 22 * * * scp node@node1:/etc/oirgin/node/node-config.yaml /admin/backup/node1/node-config.yaml; scp node@node1:/var/spool/cron/root /admin/backup/node1/root_cron
```
On the masters collect all of the files from the etcds with the following cron entry for each etcd
```shell
        29 22 * * * scp etcd@etcd:/etcd/e001.tgz /admin/backup
```
On the masters collect all of the local config files for that master.
```shell
        40 22 * * * cp /etc/sysconfig/atomic-openshift-master-api /admin/backup/master/atomic-openshift-master-api
        40 22 * * * cp /etc/sysconfig/atomic-openshift-master-controllers /admin/backup/master/atomic-openshift-master-controllers
        40 22 * * * cp /etc/origin/master/master-config.yaml /admin/backup/master/master-config.yaml
        40 22 * * * cp /etc/origin/node/node-config.yaml /admin/backup/master/node-config.yaml
        40 22 * * * cp /etc/ansible/hosts /admin/backup/master/hosts
        40 22 * * * cp /var/spool/cron/root /admin/backup/master/root_cron
```
For each master
```shell
        45 23 * * * scp -r root@othermaster:/admin/backup/othermaster/* /root/backup/othermaster/
```    
Create a tarball and send it to the backup server
```shell
        50 23 * * * tar -czf /root/m001_`/usr/bin/date "+%Y.%m.%d"`.tgz /root/backup
        55 23 * * * scp /root/m001_* cloud-user@128.31.22.168:/home/cloud-user/; mv /root/m001_* /root/sent
```
Restore process
 1. Create the VMs for the OpenShift cluster
 1. Run the ansible script to install OpenShift
 1. On each master, turn off the master processes.
 1. Update the etcd.
 1. Update the nodes
 1. update the masters
 1. start the masters.
