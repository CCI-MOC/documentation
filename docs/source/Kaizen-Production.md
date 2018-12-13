# Kaizen Production
[Trusted Users](Trusted-Users.html)

[Kaizen Backups Overview](Kaizen-Backups-\(SCC\)-Overview.html)

### Additional IP address range from CSAIL
  **VLAN 3802 128.31.24.0/22** - floating IPs and infrastructure with direct connection to Kumo/Engage1

### Additional IP address range 
The following subnet is to be used for Spring 2016 class only. These public IP addresses were to returned to NEU IS&T at the end of the class. 

  **Subnet** : 129.10.3.128/26

  **HOST RANGE** : 129.10.3.129 - 129.10.3.190

  **Broadcast** : 129.10.3.191

  **Virtual Gateway (common)** : 129.10.3.129

  **Gateway on switch 1** : 129.10.3.130

  **Gateway on switch 2** : 129.10.3.131
 
  **New public subnet** : 129.10.5.0/24

  **Gateway** : 129.10.5.1

Subnet advertisement needs to be to NEU from Cisco switches.

  **VLANS configured on both switches carrying public traffic** :
  * 125(129.10.3.0/25)
  * 126(129.10.3.128/26)
  * 127(129.10.5.0/24)

### Updates

![](https://github.com/CCI-MOC/moc/blob/master/docs/images/Updates.gif)

How update process would work:
1. A VM will pull updates from RedHat daily and create local repository in a folder named repos.
2. Staging machines will be pulling update from repos using yum-cron.
3. A cron job will start tempest test against staging machines.
4. If test is successful the folder prodrepos will be renamed to prodreposbackup and repos will be copied to prodrepos.
5. Production machines will use yum-cron and update from prodrepos - which only gets updated if tempest tests succeed on the updated staging area.

