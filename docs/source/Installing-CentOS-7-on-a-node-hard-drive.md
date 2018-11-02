**Note: Some of this information may become irrelevant once we get network booting fully figured out, Evan W. has already created a network boot image for CentOS 7 minimal**

1. Be sure you can access the IPMI console, possibly as described in [[VM-Setup-for-Cisco-IPMI-Access]].
2. Download the CentOS 7 minimal image from https://www.centos.org/download/ in the VM you can access the IPMI console from.
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step2.png)
3. Open your preferred web browser and log into the IPMI console. (Contact the ops team if you need access.)
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step3.png)
4. It may be necessary to perform a BIOS fix to CentOS 7 properly installs. First power off the machine...
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step4a.png)
... then go to BIOS -> Configure BIOS -> Advanced -> Onboard Storage, and make sure "Onboard SCU Store SW Stack" says "Intel RSTe".
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step4b.png)
5. Start up the KVM console, and click "yes" and "continue" despite all the warnings. There are extra instructions on this, depending on your browser, in [[VM-Setup-for-Cisco-IPMI-Access]]. 
Click Virtual Media -> Activate Virtual Devices, click yes on warning.
Click Virtual Media -> Map CD/DVD -> Browse -> Select CentOS 7 image -> Click Map Device.
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step5a.png)
6. Power on server, go to KVM console, and hit F6 to access the boot menu.
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step6a.png)
Choose the boot device "Cisco vKVM-Mapped vDVD1.22".
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step6b.png)
7. Choose “Install CentOS 7” from the options. 
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step7.png)
8. Unless you're better at foreign languages than me, pick the install language as English.
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step8.png)
9. Click "Software Selection", choose "Compute Node". I've only tried the default settings. 
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step9.png)
10. THE NEXT STEPS NEED TO BE FOLLOWED EXACTLY, OTHERWISE THE INSTALLER CRASHES AND YOU NEED TO RESTART FROM "POWER ON SERVER". Click “Installation Destination”.
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step11a.png)
Under "Other Storage Options" -> "Partitioning", click "I will configure partitioning."
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step11b.png)
Click "Done," which brings you to "Manual Partitioning".
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step11c.png)
If there are any listed entries besides "New CentOS 7 Installation", delete them by clicking on the listed entries then pressing the "-" button below.
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step11d.png)
Under "New CentOS 7 Installation" → "New mount points will use the following partitioning scheme:", pick "Standard Partition". (Remark: Partitioning as LVM ultimately is what causes the issue I warned about.) Next, click "Click here to partition them automatically."
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step11e.png)
Click "Done", and then in the below window click "Accept Changes".
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step11f.png)
11. Click "Network & Host Name".
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step12.png)
Depending on the node, select "Ethernet (enp130s0f0)" or "Ethernet (enp6s0f0)", -> On. Click "Done."
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step12b.png)
12. Click "Begin Installation".
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step13.png)
13. Click "Set Root Password".
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step14.png)
Set a root password as appropriate, then click "Done". (You may have to do this twice if CentOS 7 deems your password weak.)
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step14b.png)
14. Click "User Creation".
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step15a.png)
Enter a username and password. I chose to click to make the user an administrator to use "sudo" without going to "su". Next, click "Done". (You may have to do this twice if CentOS 7 deems your password weak.)
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step15b.png)
15. Wait! Your CentOS 7 install is finishing up. Click "Reboot" when it's done!
![](https://github.com/CCI-MOC/moc/blob/master/docs/images/install_centos7_min_hd_step16.png)
16. Enjoy!