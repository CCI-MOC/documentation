####Foreman VM

Foreman VM is backed up nightly at 2:35am.  The VM pauses for about 2 minutes during the backup cloning process.

The backups are located in `/var/lib/libvirt/hn/backups/foreman-backups/` on the Haas Master.

The backup script is `/root/scripts/VM_backup.sh` on the Haas Master.  There is also a copy in this repo:
[VM_backup.sh](https://github.com/CCI-MOC/moc/blob/master/scripts/VM_backup.sh)

It is run from a root cronjob which logs to `/var/log/backups/foreman-backups.log`.

The script deletes any backups that are over 7 days old, but it won't delete them if they are in a state other than "shut off".

####Openstack MYSQL database

The Openstack MYSQL database is backed up nightly at 2:06 am.  The database remains live while the backup is made, but the backup does not preserve any changes made after the process initiated.

Backups are located in `/var/lib/backups/openstack_db/` on both the production controller (node 47) and the staging area controller (node 25).

The script is found at `/var/lib/backups/scripts/openstack_db_bkp.sh` on those nodes.  A copy is also in this repo: 
[openstack_db_bkp.sh](https://github.com/CCI-MOC/moc/blob/master/scripts/openstack_db_bkp.sh)

####Remote Backups to BU's SCC Cluster   
https://github.com/CCI-MOC/moc/wiki/Kaizen-Backups-(SCC)-Overview