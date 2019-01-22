     128.52.62.182    controller public IP (and horizon dashboard IP)

     10.13.97.111     controller
     10.13.97.112     compute

Logging into the nodes themselves:

Controller and compute are both virtual machines running on kumo-dell-01.

compute node:

     $ ssh 192.12.185.143 -p 2222

For the controller node, ssh to the the public IP.

If there is some problem with the public IP, you can go to the compute node and use the 10.13.96.0/22 network.

(Eventually this should also work as a backup:  `$ ssh 192.12.185.143 -p 2221` but there is some issue with SSH between kumo-services and the controller just now. I am investigating. -Laura)

If something is really very wrong, from moc-services root account you can log into root@kumo-dell-01, launch virt-manager, and log in via the console.  If you do not have access, ask the Ops team or Piyanai. 