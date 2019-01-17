This page is to record any issues/incidents that occur.  Add the most recent incidents at the top.

Suggested format:

     ## DATE - TITLE

     ##### Description
     <short description of the issue>

     ##### Root Cause & Resolution
     <briefly describe root cause and resolution>

     ##### Troubleshooting 
     <describe significant troubleshooting steps>

     - Your Name
     
     * * *   
Also add a link to the list below.

### Incident List

* [2017 May 24 Fujitsu Ceph network OSD issues](#2017-may-24-fujitsu-ceph-network-osd-issues)
* [2017 Jan 18 Ceph HEALTH_WARN outage in Engage1](#2017-jan-18-ceph-health_warn-in-engage1)
* [2017 Jan 03 Broken floating IPs in Kaizen](#2017-jan-03-broken-floating-ips-in-kaizen)
* [2016 Dec 28 Outgoing SSH scans in Kaizen](#2016-dec-28-outgoing-ssh-scans-in-kaizen) 

***
 ## 2017 May 24 Fujitsu Ceph network OSD issues

 ##### Description
When Ceph came back up after the MGHPCC shutdown, the OSDs appeared to be stuck in a cycle going up and down, slowly losing OSDs until all remaining OSDs were located on 1 of the 4 storage nodes.  Restarting OSDs did not work.

 ##### Root Cause & Resolution
The 2 infiniband switches that are part of the Fujitsu CD10K appliance were unplugged by NEU (? or MGHPCC staff ?) in preparation for the shutdown.  This caused a disruption in the cluster network and the nodes lost contact with each other.  (Relevant interface is `bond1` on network 192.168.40.0/23).  Resolution included plugging the switches back in, then closely monitoring the cluster during recovery.  This included setting flags and controlling the up/down state of specific nodes to allow the cluster to recover in stages without the recovery I/O causing network outages and out-of-memory errors.  (Dave from Fujitsu suggested the high number of PGs in our cluster might contribute to the high I/O load during recovery.)

 ##### Troubleshooting 
- Attempt to restart all OSDs.  They came up for a while, then went down.
- Opened ticket with Fujitsu - service request 68618067
- Examined networking - about the time we discovered a networking issue Fujitsu called about it also and directed us to look at the bond1 interface
- Got access to physical machines and looked for networking issues.  Discovered 2 powered-down switches.  Plugged them in.
- Monitored the cluster as it came back up.
- A few times nodes 1-4 had to be rebooted because they ran out of memory or similar and crashed.  Console showed "out of memory errors" where it started to kill osd processes, etc.
- Working with Dave Robey from Fujitsu who also helped monitor the cluster.
- After rebooting node 3, Fujitsu set "no-up, no-down, no-out" flags so that all "out" OSDs would stay out, and no further OSDs would be marked out or up.  The intention was to let the cluster rebalance as much as it can in this state to avoid overloading the nodes with recovery I/O, then slowly add back the other OSDs.  During this stage all OSDs on node 3 would remain out of the cluster.
- Eventually the "no-up" flag was removed to allow all active OSDs to rejoin, even those from node 3.
- 6:31 pm nodes 2 & 3 went down again; rebooted and recovery continued.
- 7:14pm node 1 found to be down, rebooted
- Fujitsu turned off node 1 temporarily to assist with recovery
- 8:20pm cluster returned to HEALTH_OK status

 - Laura
 
 * * *   
## 2017 Jan 18 Ceph HEALTH_WARN in Engage1
##### Description
Engage1 Ceph was in HEALTH_WARN status reporting multiple pgs degraded and only 81 OSDs of 90 up. Lucas reported that Engage1 Horizon was very slow / not 100% functional as a result.
##### Resolution
Missing OSDs were from ceph-lenovo10, which received a kernel update this morning and failed to come back up after the reboot.   Rebooted ceph-lenovo10 and allowed Ceph to heal itself back to HEALTH_OK.
##### Troubleshooting
* `ceph -s` showed 90 osds: 81 up, 81 in.  There are 9 OSDs per server so typically if some multiple of 9 are down, a server has gone down (or lost its cluster network connection).
* `ceph osd tree` showed which OSDs were down - it was the 9 belonging to ceph-lenovo10
* Found ceph-lenovo10 to be down, appeared to not have booted correctly.  Rebooted via IPMI, this fixed the problem.
* After about 30 minutes Ceph finished recovery process and was back to HEALTH_OK

##### Notes
It takes Ceph some time to recover itself, need to keep checking `ceph -s` output.  Number of pgs stuck, degraded, backfilling, etc. should be decreasing as it recovers.

-Laura Kamfonik

***

## 2017 Jan 03 Broken floating IPs in Kaizen 

##### Description: 
Some users reported that they were no longer able to reach existing floating IPs in the NEU Kaizen cluster.

##### Resolution
After I sent some information about the problem, Garret (CSAIL) found a config mistake on his end, fixed it, IPs started working again.  

##### Troubleshooting
* reproduced the problem - my own project had 1 working and 1 non-working floating IP.  
* Asked Garrett (CSAIL) in a reply about the earlier [SSH scan issue](#2016-dec-28-outgoing-ssh-scans-in-kaizen) asking if he had changed any config in response which could have caused the issue. 
* tested basic steps like removing/re-adding a floating IP, allocating a new one, removing re-adding security groups.
* checked logs for error messages after doing the above - none found, but the IPs still didn't work
* alerted users that this was a known issue, asked them to report what IPs they had which didn't work vs. worked.
* checked that the security groups were appearing properly in the VM's iptables chain on the host
* tried migrating a problem VM to the same host as working VM
* pinged the broken IP while looking at tcpdump on both the VM host and inside the broken VM.  Found that the pings were getting into the VM just fine and it was replying, and the host could see the replies passing out its interface.
* At this point more users had replied and the pattern of .24 IPs working and .25 not working was established.  
* Sent new email to both Garrett and Anand (NEU) with all this additional info, asking whether they might have something configured to allow 128.31.24.0/24 by mistake, instead of 128.31.24.0/22.  Garrett was travelling so had not seen the email I sent earlier in the day.  He checked his config and resolved the issue.

##### Notes
Problem appeared over intersession, was not dealt with until we came back.  By next year we may want to have some kind of on call system to deal with intersession issues since the period is so long.  Or at least send out a notice to users that we are all on vacation.

-Laura Kamfonik

***

## 2016 Dec 28 Outgoing SSH scans in Kaizen
##### Description
Received an email from Garrett Wolman that he had detected an outgoing SSH scans from 128.31.24.167
##### Resolution
Shut down the VM, informed Garrett, he unblocked the IP.
##### Troubleshooting
* identified the VM as belonging to hpc project, suspended VM, sent email to Rajul
* found out from Rajul that this VM nad some others had remote password login enabled.  Shut down the other VMs too in case they had been compromised by the first one.
* instructed Rajul to build the VMs back up from scratch and not to enable password login    

##### Notes
Need publicly posted security practices that we expect users to follow in their VMs

-Laura Kamfonik
