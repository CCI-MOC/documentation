First, fix the time:
`yum install ntp`
`ntpdate`
`reboot`

Then install packstack as normal, with `packstack --allinone`, and reboot again.  At this point, the HTTP interface to openstack should be functional.