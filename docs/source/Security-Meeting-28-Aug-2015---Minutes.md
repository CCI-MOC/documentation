####1. Current CSAIL Practices
(this was essentially a Q & A with Jon P from CSAIL)
- CSAIL faces constant, but generally non-targeted attacks via SSH
- They have not experienced targeted attacks on their OpenStack cluster
- Typical attacks are just people trawling for a chance to exploit known vulnerabilities

**CSAIL standard practices**
- secure the hosts
- look at logs for uneven spikes
- they do not currently use any honeypots
- they use sshguard "all over the place"
- limit outbound access to internal networks with iptables
    - internal networks = the whole building, there are a number of IPs; it's not restricted to specific admin workstations
    - Nothing currently prevents someone from coming onsite and plugging into their own port to bypass this
    - A number of non-Openstack nodes are also on this network, such as Linux workstations used by students

**What CSAIL does with successful attacks**
- These nearly always involve one of the following:
    - Network services poorly written by a grad student
    - Web applications poorly written by a grad student
    - end users who have used the same password/credentials somewhere less secure, and they were compromised
- How they know a machine is affected
    - Spikes in inbound or outbound traffic
    - Sometimes an internal report from one of their systems
- When a successful attack is detected
    - reinstall OS
    - in the past, they have not worried about rootkits, etc. but it is a possibility
    - The level of response/analysis on staff/resources, and also which system was attacked
        - on a web server, they track down what CGI scripts caused the problem and who wrote them
        - anything running arbitrary code that users write, gets looked at more closely to make sure the relevant code is not run again after OS reinstall

**Jon P's suggested baseline for MOC security procedures**
- Regular patch schedule
- use good passwords
- for passwords you never type, 32-char random pass key string
- enforce reasonable password complexity for users

***

####2. Network Design
- Initially, we are looking at 2 extremes
    - put it all on the Internet, with selective filtering
    - or, only nodes that definitely need access to the internet are publicly accessible.

- Separated Control Plane
    - When networking the physical host, an isolated control network is good practice
    - API endpoints on the internet are needed for people to talk to the server
    - Right now, CSAIL's internal and external endpoints are the same, there is not a separate internal network.  

**Ceph Storage Networking**
- CSAIL Ceph has a backend network which is used for replication, and a frontend user network which is open to the internet
- MOC Fujitsu Ceph has 4 networks: storage, replication, management, and a network used by Fujitsu.  None are publicly accessible.
- Ceph NIC distribution proposed (this is what CSAIL has right now)	
    - one for OBM that doesn't go through the OS
    - one for external data network 
    - one for internal replication
- There has not been a case at CSAIL where Ceph had such high bandwidth demand that they couldn't get in
    - if that happened, you could still get a virtual serial console through the OBM.
- Lenovo Ceph we will have two pools:
    - Production
    - Big Data
- Ceph does not encrypt data network traffic
- Suggestion: Data should be separate from the public interfaces

**Ceph Storage Vulnerabilities**
- If a compute node is compromised, the attacker will be able to see all the data going in and out.
    - It could be possible to design things so that there would still be components of the system that we trust.
- A successful attack on a compute node leaves Ceph vulnerable
    - the attacker could do anything Ceph can do, such as delete all data
- Suggestion: Have a backup system that allows going back 1 day, or similar
- Suggestion: put data traffic on a separate VLAN, so it can be encrypted
    - This would not work.  Compute nodes have a Ceph key that has access to a large set of the Ceph data, and if attackers got this they could use it to get the data regardless of encryption
- Suggestion: Isolate the VLAN traffic for each of the 2 openstack environments, so that if one is compromised the other is not.
    - Possibly not something that can be enforced
    - Two public IP addresses.  Each side gets access to one, and not the other.
        - to do this on Fujitsu, would require messing with the internal Fujitsu switch
	- Fujitsu does not need to support 2 environments in the near term, but we can explore this on Lenovo
	- the switch could be configured for DHCP snooping

*NB: Here there was a brief discussion that I did not fully capture in my notes\:*

     Brief mention of network pass through; it was decided not to worry right now about this
     Use VLAN to create an isolated network over which VXLAN traffic will flow

**NETWORK DESIGN RESULT**
- Components
    - IPMI network for non-haas-managed resources
        - In terms of multiple CSAIL/MOC IPMI, if there is no security concern, we can do what makes the most sense.  
        - Stuff in the same pod can be on the same IPMI network, stuff in different pods can have its own IPMI network
    - separate IPMI network for haas-managed resources
        - We can create non-administrative IPMI users for haas, which only have access to the specific set of actions needed by haas, not full access to IPMI.
    - external API endpoints - horizon, rgw
    - internal API endpoints  
    - storage data VLAN
    - storage management VLAN
    - neutron networks (vxlans)	
- A second NIC may be useful for monitoring but this will be discussed later as it is not a security issue.


**Access to IPMI from the OS**
- Can we / should we disable access to IPMI from OS?
- CSAIL has dedicated IPMI NICs, but also has OBM access from inside the OS 
    - this is useful to access things like sensors
    - however, if the OS is compromised, this allows flashing the firmware, as well as OBM access to other machines
- Suggestion: the OS should be a limited-access user, like we discussed above for haas
    - No.  Credentials are only checked for remote access, so this is not possible
- Suggestion: Identify the uses of this, and see if there is a better way to do it that doesn't require accessing IPMI from the OS
    - e.g. there are utilities to get at sensor data
    - one thing CSAIL uses internal IPMI access for is to set up remote IPMI access, but this is a one-time task
	
**Switch Configuration**
- MIT's external networking setup should be rejecting things like BGP before it gets to us
    - However the switch might be listening on that port, which could in theory be exploited
- Suggestion: we should turn off everything we have no use for on the switches
    - Does CSAIL do this?
        - it depends on the switch, but typically they start clean and make a new config
        - Garrett has a standard set of configs that he pushes, with tweaks to the IP addresses, etc
    - UMASS does something similar to CSAIL
        - Joe can get us some of their standard configs to look at
    - Joe would like to talk to the person at Northeastern who configured the switches, because they have routing capability 
        - they are currently routing the 192.168.28 network used by Fujitsu
        - Reportedly the switches were using a standard NU in-house config, with some extra things turned on for us by Anand
    - Suggestion: Only turn on the very specific things we need, for both switches and router
        - We don't own the current router, NU owns it 
        - Once we use the MIT router instead, we will have more control
        - Jon P: Once we get a list of VLANs together, we can talk to Garrett and get some numbers.  
        - Everything will have to be reconfigured after switching to the MIT router
- Discussion: Are we concerned that the switches might have gotten broken into?  
    - They were previously open to the internet with a bad password.  This has been fixed and the password changed. 
    - They had old firmware with known vulnerabilities. Nothing has been done to fix any lower-level attacks that might have happened.
    - Question to Jon P - how comfortable are you with this?
            - Update the firmware if possible, to eliminate those known vulnerabilities, that should be fine.
- Consensus on what to do:    
    - write new configs from scratch
    - only trunk shared vlans up
    - upgrade firmware
    - Joe has experience with these switches, so if he gets specs from Garrett he can work on them

***

####3. Responsibility for infrastructure
We have 3 sets of infrastructure - who is in charge of hardware, firmware, etc?
- CSAIL 
    - CSAIL takes care of all their own stuff
- Northeastern 
   - MOC team in charge of OS stuff
   - NU will do firmware, but we need to talk to them about how proactive they will be   
- Harvard 
   - we haven't talked to them in a while, but we should also ask them to proactively upgrade firmware
- In theory, the person with the support contract is the one who should be doing the firmware etc.
    - We need to clarify this with NU and HU.

***

####4. Route to the IPMI network
How do we keep this accessible while protecting it from attacks?
- Suggestion: 1 hardened box that you SSH into, then from there to the OBM
    - we should have 2, one as a backup
    - we currently have haas master, and cisco-emergency-recovery as a backup  
- No concerns with CSAIL having access to all the IPMI networks as well
- Suggestion: Could our hardened box be a virtual machine?
    - No. There's too many requirements before the VM is actually working, we want something that works low level.
    - Better to use a cheap box
- Graphical access to the NU IPMI requires outdated flash and java, so that is best done from a VM 
    - some of the BIOS stuff requires the graphical console, which is why we have this special VM.
    - On CSAIL Dell equipment, they can SSH to the DRACs and run 'console com2' to get a virtual console, which is easier
        - Maybe we did not fully explore alternatives and there is some way we can do this too
	- However CSAIL's stuff is in the building, so they don't use a remote solution when going into BIOS.  
        - The stuff CSAIL has at MGHPCC is dumb and does not require a graphical interface
- Suggestion: a few hardened boxes that are not very powerful, running VNC servers
    - you SSH into the boxes
    - you then can then run a VM for the outdated flash/java stuff in it anywhere you want, and use port forwarding with VNC

***

####5. Publishing Security Practices
Should our practices be recorded on the public wiki, or do we keep them private?
- In theory, if our stuff is well locked down, recording how we locked it down doesn't matter
- Consensus: for the stuff we discussed today, the public wiki is probably fine
    - There may be some things we need to put on the private wiki later
