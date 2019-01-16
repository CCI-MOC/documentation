# Provisioning Machine Installation Instructions
This document lists the steps to install Foreman, initially meant for our Northeastern Environment.

### Install VNC Server
We first want a VNC server to be able to get desktop access, in order to be able to use virt-manager.

Install the package

	yum install tigervnc-server

Copy the sample conf file to `/etc`

	cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@.service

Add firewall rule to accept connections to port 5910 for vnc

```
iptables -A INPUT -p tcp -m tcp --sport 5910 -j ACCEPT
iptables -A OUTPUT -p tcp -m tcp --dport 5910 -j ACCEPT
servicectl iptables save
```

Download centos disk image

