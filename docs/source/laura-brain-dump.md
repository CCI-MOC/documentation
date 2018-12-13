## Left on my desk in the Team Room at BU:

* the 4 10G nics leftover from the box Rado brought to MGHPCC last time
* the label maker and extra cartridges (we usually order new cartridges from Amazon)
* a 3-prong-to-C14 adapter that is supposed to live in the red crate at MGHPCC
* a handful of short (2 or 3 feet) C13-to-C14 power cables

## MGHPCC Notes

* Brocade Switch in R4PAC04 is full - there are no more ports after installing the additional Intel servers.  We will need either an additional Brocade switch there, or to install additional compute nodes in C02 instead where there are 3 Brocade switches and plenty of ports.  (See notes below about "spare" Brocade switches)

* Kumo networking is not fully labeled.  Would be good to get labels there and also audit to make sure the cable map spreadsheet is accurately.

* Something I never got around to setting up properly: [[PDU Documentation]].  Remote access to the PDU is useful because you can "unplug" servers if needed to reset by powering down a specific port.  I did configure some of them in Engage1 but I'm not sure if there is up to date documentation - probably best to just restore them to factory settings as described at the link, which can be done without interrupting the power supply to servers, then reconfigure them as you like.

* We tried to find the CSAIL switch that we are connected to, one cable went to R5-PB-C18 u39 and the the other to R5-PB-C18 U38, but there wasn't anything in that cabinet/U when we looked?  Double check that we looked in the right place, and then maybe ask CSAIL if they moved it.


## Links to documentation people may find helpful:

* [[Helpdesk VMs - Index | Helpdesk-VMs---Index]] - this is a list of links to other pages about the Helpdesk VMs and how they work
* [[MGHPCC]] - brain dump of information about MGHPCC
* [[Setting up the RADOS gateway]], [[Talking to the RADOS gateway]], and [[Specifying Pools via Radosgw]] all contain information of interest although may not be 100% accurate with latest releases
* relevant to specifying pools via Radosgw - I saw some evidence you may be able to configure a default placement target by project.  I have not looked into this closely.  Rado, this may be of interest to Ugur's work?

## MOC-bot
We had a bot that is supposed to sit in the #moc IRC channel and collect logs.  The VM running it got deleted a while back to free up resources.

It would be good to recreate the bot, this time running in an OpenStack VM inside the moc-infrastructure project.  Best bet is probably to set it up from scratch so you understand how it works:
1. create a small VM (m1.small is plenty)
2. Deploy supybot/Limnoria, documentation is [here]([http://doc.supybot.aperio.fr/en/latest/)  
3. Try to set things up so the logs are pushed up to Ceph in the moc-infrastructure account instead of being stored on the VM.  You will probably need a script for this; there are some basic examples of how to use Python to interact with Ceph in the MOC Openstack Tutorial.

The original bot VM's disk is still there on node 39 it's the lvm /dev/vg2/logger-bot on node 39.  Rado launched a new VM from it.  I was going to try to recreate it in OpenStack on my last day but did not have time.  There is some really outdated documentation about it at [[#MOC Channel Logs]] and in the old [[#Ops Log]] but basically the only thing that is still accurate there is the fact that it's a Limnoria bot - I mention it just in case there is something there that helps, but a lot of it dates to when the VM was on moc02 in PRB.

I extracted the old logs and put them in the Object Store under the moc-infrastructure project on Kaizen.

We want to be able to completely delete the old VM from node 39, but I would leave it as an example for now until the new one is working.  Austin & Rado, I added your keys, so you should be able to SSH to it from node 39 like this:

ssh moc-ops@10.13.37.5

(Austin you probably need Rado to give you an account on 39 which is the services node in Kaizen)

These credentials also work to log into the console:
user: moc-ops
password: channel!logs15

moc-ops user has passwordless sudo.

I don't remember much about the mechanics of how the bot starts up, and it doesn't seem to have started up automatically when we recreated the VM.  

## Other stuff

* We have 3 "spare" Brocade switches that are not part of the fabric.  One is on loan to Alina and Gen, one is used by HIL folks in PRB, and one is the "OpenFlow" switch installed in R4PAC04 but I really don't think the researchers are using it.  The OpenFlow switch was upgraded to version 7 of the operating system so that it could support OpenFlow while all other switches are running version 5.  Details of its config are here [[https://github.com/CCI-MOC/moc/wiki/OpenFlow-SDN]] which may be out of date?

