# Policies

* As we decide on user/network/other security policies, they should go here.

# Tasks

## Priorities

Each task should be labeled with one of the following priorities:
* **Phase 0**: Highest priority; Necessary to have basic levels of security.
* **Phase 1**: Necessary, though only done after Phase 0 tasks completed.
* **Phase 2**: Will enhance security, but are longer-term focused. May provide additional functionality.

## Policies

Policies need to be written to cover:
* MOC Users:
 * This includes such things as who can obtain accounts, how we verify their identity, default quotas, etc.
 * HaaS *Phase 1*
 * OpenStack *Phase 0*
 * Acceptable use policies (should probably incorporate originating institutions) *Phase 1*
 * Password requirements (complexity, how often need to be changed)
* MOC admins *Phase 1*
 * Where admin changelogs are kept
 * Who needs to approve what types of changes
 * Who is responsible for which components

## Networks

Each VLAN for now will not be routed. A *Phase 2* project would be to examine
how we could use routing to simplify access.  With OpenStack, we are separating
the control plane from anything user-accessible. By having separate VLANs, if
we notice that some are starving others (like Storage), we can set up QOS.

* OpenStack divided into separate VLANs *Phase 0*:
 * Control-plane:
  * Storage
  * Internal API
  * External API
  * Neutron networks/carries VXLans
  * Non-HaaS OBMs (Fujitsu and Lenovo appliances)
 * Data-plane/User-accessible
  * Configure default security group/OpenStack firewall to only allow incoming connections from the 5 universities *Phase 0*
   * Have an alternative group that is world-accessible *Phase 2*

* Other VLANs:
 * OBM HaaS (Cisco UCS IPMI interfaces) *Phase 0*
* Enabling DHCP Snooping to prevent ARP spoofing/Rogue DHCP servers *Phase 1*
  * GAW notes: what is the threat model that this is intended to address?
  * henn: preventing a compromise of one machine from enabling that machine to conduct [nasty attacks against other nodes](https://en.wikipedia.org/wiki/Arp_spoofing)
* Discuss/implement mechanism for remote access to isolated networks *Phase 0*
 * Bonus points if it integrates with the centralized identity *Phase 1 or 2*
* Mechanism to access HaaS-allocated networks  (Jason/VPN appliance) *Phase 0*
* Mechanism to access other private networks *Phase 0*
* GAW notes: CSAIL infrastructure is providing two routed, public networks, a "client" network and a "hardware" network; all CSAIL systems are expected to be on the "hardware" network (128.52.60.0/22), but we have yet to figure out the story on the "client" network (28.52.64.0/18) and who gets access to it. My working theory is that physical nodes get IP addresses assigned by the institution that owns that hardware.

## Network services

* Intrusion Detection/Prevention (Snort/[Guardian](http://www.chaotic.org/guardian/) is an open source stack) *Phase 1*

* Network monitoring: need to decide *Phase 1*
 * Record port bandwidth statistics
 * Maybe record IP connections?
 * Use logstash?

### Switches

* Write hardened/new switch config from scratch that disables all non-necessary services (Joe) *Phase 0*
  * GAW notes: CSAIL has a bunch of different switches, some of which are/will be integrated with CSAIL management -- the stuff OpenStack is directly connected to is weird and only our RFC1918 private network for in-band management

## Puppet/OS Management

Puppet initially will be used for just the OpenStack nodes. Eventually, it
should be used for *all* MOC nodes, since it will install several
security-beneficial tunings.

* Centrally-managed active puppet server to manage all nodes *Phase 0*
 * This should be a locked down machine since it can manage all others.
 * Server accessible from non-OpenStack networks *Phase 1*
 * Removal of modified puppet modules (like ssh) and directly using upstream *Phase 2*
 * Ubuntu support *Phase 2*


### OS services to harden

* sshd *Phase 0*:
 * disable remote root, disable password access
 * include a restricted set of authorized keys for root
 * include other users based on need
* sshguard *Phase 0*
* Install encrypted user passwords using puppet *Phase 0*
* Enable sudo access for small list of admins
* systemd/syslog/haas: Send all logs to central log server *Phase 0*
* Set timezone *Phase 0*
* NTP: sync to MIT/BU servers (time.mit.edu, ntp{1,2,3}.bu.edu) *Phase 0*
 * GAW notes: sync to four reasonably close-by servers (latency much less important than jitter so avoiding public or roundabout network routes is beneficial). Equipment on the CSAIL network will sync to the CSAIL servers, etc. Jim @ MGHPCC is looking at setting up a public (tenant-facing) NTP server which should be used by Holyoke hardware once clients have a direct routed connection to MGHPCC tenant infrastructure networks.
 * Sync to MOC time servers *Phase 1*
* Auto-install security patches. Rebooting at a convenient time if needed *Phase 0*
* Firewall rules *Phase 0*
* Entropy-gathering on hosts (rngd from rng-tools) *Phase 1*
 * Might have to modprobe intel-rng or tpm-rng (tpm requires enabling TPM in bios)
* Disable non-essential services (research this more) *Phase 2*
 * Includes: postfix
* Other packages we want installed *Phase 1*
 * mtr (nice traceroute)
 * mlocate (enables `locate` command)

## User VM visible

* Share entropy with guests via [virtio-rng](http://wiki.qemu-project.org/Features-Done/VirtIORNG) after improving host entropy gathering with rngd (see above) *Phase 2*
* Document MOC time servers and mirrors for users *Phase 2*

## Central network services

* Set up network log server for OpenStack *Phase 0*
 * Make it accessible to non-OpenStack networks (IPMI(?), switches/routers, apache?) *Phase 1*
* MOC time server *Phase 1*
 * Must be a physical machine because time keeping is poor in VMs
 * GAW notes: if you want your own time server, just buy one, they don't cost that much and will keep more stable time than any whitebox Intel server (installing the requisite add-ons to a whitebox server will cost you more than just buying the right thing from a company that makes them -- I recommend EndRun Technologies, who make time servers that can synchronize to the IS-2000 CDMA phone network and thus don't require a roof antenna for GPS)
* RHEL/other distros, so that machines do not need external/NAT access to update *Phase 1*


## Updating

We need procedures in place to stay up to date, patching our various softwares proactively to address security issues. This includes:
* Cisco switches *Phase 0*
* Cisco UCS firmwares *Phase 0*
* RHEL OS's *Phase 0*

## Credentials

* Need a credential-tracking system for when admins and users want to log into various systems: IPMI, users, routers, higher-level services like horizon, etc. *Phase 1*
 * There are two aspects of this:
  1. giving admins access to passwords when needed *Phase 0*
  2. Federating identity directly to devices (single-sign on) *Phase 1 or 2*
 * Could be PGP encrypted files checked into git, puppet, LDAP, RADIUS, kerberos, etc.
  * GAW notes: password-store is a huge win and already integrates with GnuPG and git
  * GAW notes: at CSAIL we use Kerberos for everything, but sysadmins have ssh-based back door for login access (this does not give access to home directories); we have our own private-label X.509 CA for web app auth (looking to potentially migrate this to RHCS or dogtag)
  * GAW notes: we've also started thinking about SAMLv2 federation stuff (e.g., mod_auth_mellon) but it looks like an incredible PITA compared to TLS certificate auth for our in-house use cases

### IPMI
* Create randomized/different admin accounts. Store in the credential-tracking system *Phase 2*
 * GAW notes: some BMCs and the Dell DRAC allow ssh keys for remote access, with weird and hard-to-program restrictions on key types and number of keys per principal
* Create low-privilege accounts for:
 * HaaS (needs access to just: serial console, reset, power off/on) *Phase 0*
 * Research purposes (read-only; for looking at power stuff) *Phase 1*

## Hardware

* BIOS
 * disable OS upgrades *Phase 0*
 * Can we disable local IPMI? *Phase 0*

## Pro-active security measures

* Setting up [honeypots](https://en.wikipedia.org/wiki/Honeypot_%28computing%29) *Phase 2*

# Other planning

* Network layout going forward. Firewalls, NATs, firewall appliances, etc.
 * What should the production OpenStack look like? How do user VMs get to the Internet?
 * A system-wide firewall
   * GAW notes: our policy at CSAIL has always been "hosts are responsible for their own security"... this is particularly the case in a hypervisor environment where it must be assumed that the tenant VMs are hostile -- so count us as being opposed to this without a clear discussion of the threat model and desired access policies (in particular, our research users expect to be able to do whatever they want on the network, with very few restrictions)
 * Other HaaS users
    * What are the core network services we plan to offer?
      * DNS, DHCP, NTP
      * Do we offer a RHEL/other distro mirrors?

* Security plan
  * Managing credentials
   * Can we have a non-privileged IPMI user for most tasks
   * Encrypting admin credentials
   * Hardening systems
     * Includes IPMI, switches, ...
* SSH key management
  * Should we do this with puppet?
    * GAW notes: we do at CSAIL, but puppet has some really annoying design fla^W^Wlimitations in how the authorized_keys provider actually works... Kerberos with .k5login is actually much easier to deal with in puppet and is easier to centralize key revocation
  * If so, how do we secure the puppet server?
    * Does LDAP/some other password dist system make sense if no one typically logs in with passwords anyway
      * GAW notes: LDAP is your directory, don't put passwords in it
  * Better logging
    * Right now, all we know is that certain systems sent lots of packets. No idea if they broke into the system (and if so, whether they got root), found an amplification attack, etc
    * Depending on the network layout, should we run an intrusion detection system like Snort on the gateway or through a mirror port?
    * Setting up a honeypot on non-public networks to detect breakins
    * Keeping various software up to date (switches, firmwares, OS's)

* Remediation of the cluster now that we believe it's been broken into.
  * How do we clean the machines (especially BIOS, disk firmware, etc)
   * Which University's incident response group should we work with?

# From Piyanai (was in _Footer):
Which "broken in" are you referring to Jay ?

The only security related incident I are aware of is the 2 large network packets egress traffic incidents on a particular node a few weeks back. For those incidents, we worked with the NU network folks and took steps to mitigate. And we also agreed for you to head up a security effort as a proactive measure i.e. this wiki page. 

Are you considering these 2 incidents a "broken in" ?

