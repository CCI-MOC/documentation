Whenever configuration changes are made to these VMs, it's a good idea to save a snapshot.

### Kaizen Helpdesk VM

**NOTE: This is the helpdesk VM used by UMass helpdesk**

The vm is `moc-helpdesk` in the `moc-infrastructure` project on Kaizen.  Currently Rado and Austin have access to this project, and to the VM.

IP: 128.31.25.252

Console login: 

user: `root`
password: `BsuYAi1HC53omFFVo+`

The helpdesk operators log in as `helpdesk` user.

The VM has yum-cron enabled for security updates.

***

### Engage1 Helpdesk VM

IP: 128.31.22.62

Console login: 

user: `centos`
password: `KWWA44dIKyEx`

This VM is running Setpass on port 5001.  Relevant `/etc/setpass.conf` and `/etc/httpd/conf.d/setpass.conf` are in the private `moc` repo along with the other site-specific helpdesk config files.