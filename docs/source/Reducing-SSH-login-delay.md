##Info:
Setting ```UseDNS no``` in the VM's ```/etc/ssh/sshd_config``` or ```/etc/sshd_config``` has made it much faster to ssh into VMs.

centos@128.31.24.158 is a m1.small VM in Kaizen with a public IP.

With ```UseDNS yes``` (the default in Ubuntu 16.04), ```ssh -A centos@128.31.24.158 'exit'``` (ssh into the VM and then immediately exit) takes **5.8 seconds**.


With ```UseDNS no```, ```ssh -A centos@128.31.24.158 'exit'``` takes **248 ms**. So changing this option shaves 5.5 seconds off the time it takes me to ssh into a VM.

More info about the UseDNS option to see if you actually need it enabled:
* http://unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option
* https://bugs.launchpad.net/ubuntu/+source/openssh/+bug/424371

##How To:
1. ```sudo vim /etc/ssh/sshd_config``` or ```sudo vim /etc/sshd_config```
2. Change ```UseDNS yes``` to ```UseDNS no``` or add ```UseDNS no``` to the file, then quit vim
3. Restart ssh ```sudo service sshd restart```