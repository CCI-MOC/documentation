### 2017-07-22 asmith

At 11:30EST, the following alert was triggered, indicating that the helpdesk VM is down:
```
FROM: nagios@e1-nagios.engage1.int.cloud
DATE: July 22, 2017 at 11:30:56 AM EDT
TO: alerts@lists.massopen.cloud
SUBJECT: [ALERTS] ** PROBLEM HOST ALERT: HELPDESK_VM IS DOWN **
***** Nagios *****
Notification Type: PROBLEM
Host: Helpdesk_VM
State: DOWN
Address: 128.31.25.252
Info: CRITICAL - Host Unreachable (128.31.25.252)
Date/Time: Sat Jul 22 11:30:56 EDT 2017
```

I investigated, and came to the following partial conclusions by 1443EST:
```
The helpdesk VM alert that occurred at 11:30EST was caused by a kernel
panic at 11:28EST, as a result of a system clock synchronization
failure. The system clock was off by ~127 seconds. This appears to
possibly be an issue with NTP, since the logs show the error message
"Can't synchronise." I'm looking into it further. The relevant log
output is below. The VM is up right now, and has remained up since
recovering shortly after the 11:30EST alert. The times below are UTC,
naturally.

Jul 22 15:30:19 moc-helpdesk chronyd[602]: Can't synchronise: no majority
Jul 22 15:30:24 moc-helpdesk chronyd[602]: Source 52.6.160.3 replaced
with 129.250.35.251
Jul 22 15:30:31 moc-helpdesk chronyd[602]: Selected source 45.79.187.10
Jul 22 15:30:31 moc-helpdesk chronyd[602]: System clock wrong by
127.738875 seconds, adjustment started
Jul 22 15:31:23 moc-helpdesk chronyd[602]: Selected source 64.251.10.152
Jul 22 15:31:23 moc-helpdesk chronyd[602]: System clock wrong by
36.573980 seconds, adjustment started
Jul 22 15:31:36 moc-helpdesk chronyd[602]: Selected source 45.79.187.10
Jul 22 15:31:36 moc-helpdesk chronyd[602]: System clock wrong by
2.704115 seconds, adjustment started
Jul 22 15:32:28 moc-helpdesk chronyd[602]: Selected source 64.251.10.152
Jul 22 15:32:28 moc-helpdesk chronyd[602]: System clock wrong by
3.584265 seconds, adjustment started
Jul 22 15:32:40 moc-helpdesk chronyd[602]: Selected source 45.79.187.10
Jul 22 15:33:32 moc-helpdesk chronyd[602]: Selected source 64.251.10.152
Jul 22 15:33:32 moc-helpdesk chronyd[602]: System clock wrong by
3.605428 seconds, adjustment started
Jul 22 15:33:38 moc-helpdesk chronyd[602]: Selected source 129.250.35.251
Jul 22 15:33:38 moc-helpdesk chronyd[602]: System clock wrong by
-4.212040 seconds, adjustment started
Jul 22 15:39:08 moc-helpdesk dhclient[799]: DHCPREQUEST on eth0 to
10.11.12.2 port 67 (xid=0x33e82953)
Jul 22 15:39:08 moc-helpdesk dhclient[799]: DHCPACK from 10.11.12.2
(xid=0x33e82953)
Jul 22 15:39:10 moc-helpdesk dhclient[799]: bound to 10.11.12.7 --
renewal in 32483 seconds.
```

By 2102 EST I had arrived at the following conclusion:

I've been able to confirm that the helpdesk VM incident at 11:30EST was caused by a faulty system clock. This fault caused a kernel panic, which triggered the alert. The system was able to recover itself because chronyd was able to connect to NTP servers and correct the system clock. The system clock is now running ~0.000007653 seconds fast of NTP time, which is not causing problems, and which I assume is within operational norms.

The relevant logging and system status information is below.

I have still not determined the cause of the faulty system clock.
```
[root@moc-helpdesk ~]# systemctl status chronyd.service
● chronyd.service - NTP client/server
   Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2017-07-21 20:17:16 UTC; 1 day 3h ago
  Process: 612 ExecStartPost=/usr/libexec/chrony-helper update-daemon (code=exited, status=0/SUCCESS)
  Process: 600 ExecStart=/usr/sbin/chronyd $OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 602 (chronyd)
   CGroup: /system.slice/chronyd.service
           └─602 /usr/sbin/chronyd

Jul 22 15:31:36 moc-helpdesk chronyd[602]: System clock wrong by 2.704115 seconds, adjustment started
Jul 22 15:32:28 moc-helpdesk chronyd[602]: Selected source 64.251.10.152
Jul 22 15:32:28 moc-helpdesk chronyd[602]: System clock wrong by 3.584265 seconds, adjustment started
Jul 22 15:32:40 moc-helpdesk chronyd[602]: Selected source 45.79.187.10
Jul 22 15:33:32 moc-helpdesk chronyd[602]: Selected source 64.251.10.152
Jul 22 15:33:32 moc-helpdesk chronyd[602]: System clock wrong by 3.605428 seconds, adjustment started
Jul 22 15:33:38 moc-helpdesk chronyd[602]: Selected source 129.250.35.251
Jul 22 15:33:38 moc-helpdesk chronyd[602]: System clock wrong by -4.212040 seconds, adjustment started
Jul 22 15:53:12 moc-helpdesk chronyd[602]: Selected source 209.242.224.117
Jul 22 16:15:45 moc-helpdesk chronyd[602]: Selected source 45.79.187.10
[root@moc-helpdesk ~]# chronyc sources
210 Number of sources = 4
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^* tock.no-such-agency.net       2  10   377   939   -362us[ -359us] +/-   34ms
^+ y.ns.gin.ntt.net              2  10   377   934  +1990us[+1990us] +/-   46ms
^+ undef.us                      2  10   377   674   -554us[ -554us] +/-   84ms
^+ borris.netwurx.net            2  10   377   471  -1130us[-1130us] +/-   38ms
[root@moc-helpdesk ~]# chronyc tracking
Reference ID    : 45.79.187.10 (tock.no-such-agency.net)
Stratum         : 3
Ref time (UTC)  : Sun Jul 23 00:32:41 2017
System time     : 0.000007653 seconds fast of NTP time
Last offset     : -0.000008498 seconds
RMS offset      : 0.350177914 seconds
Frequency       : 81.827 ppm slow
Residual freq   : +0.000 ppm
Skew            : 0.006 ppm
Root delay      : 0.021135 seconds
Root dispersion : 0.015891 seconds
Update interval : 1031.3 seconds
Leap status     : Normal
```
In case you're worrying that NSA screwed us with faulty NTP time, not to fear, it's just Google's NTP service:
```
   Domain Name: NO-SUCH-AGENCY.NET
   Registrar: GOOGLE INC.
   Sponsoring Registrar IANA ID: 895
   Whois Server: whois.google.com
   Referral URL: http://domains.google.com
   Name Server: NS-CLOUD-A1.GOOGLEDOMAINS.COM
   Name Server: NS-CLOUD-A2.GOOGLEDOMAINS.COM
   Name Server: NS-CLOUD-A3.GOOGLEDOMAINS.COM
   Name Server: NS-CLOUD-A4.GOOGLEDOMAINS.COM
   Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Updated Date: 13-apr-2017
   Creation Date: 13-apr-2002
   Expiration Date: 13-apr-2018

https://developers.google.com/time/
```
******

### 2015-10-12 ravig

Disabling defroute

In few of the nodes, we need to update the defroute parameter so that the default route out of the node is through this interface(say in case of public interface)
```
BOOTPROTO="dhcp"
DEVICE="enp130s0f0.1053"
HWADDR="90:e2:ba:84:d5:70"
ONBOOT=yes
DEFROUTE=yes
PEERDNS=no
PEERROUTES=no
VLAN=yes
```
This can be checked using ip route
 ip route
```
default via 172.16.0.1 dev enp130s0f0.1053 
```


### 2015-08-12 gsilvis

Here's a way to test that you have ceph pools and authorization set up right.
```
rbd --keyring /etc/ceph/client.clustername-openstack.key --id clustername-openstack ls clustername-ephemeral-vms
```
If it errors, something's wrong.  If it prints nothing, then the pool is empty, which is probably what you expect.

### 2015-08-04 gsilvis

Here's a script to make three Ceph pools on the Fujitsu hardware.  It's available in root's home directory on the Fujitsu administration node as well.
```
    #!/bin/bash
    
    if [ $# -ne 1 ]; then
        echo "Please pass exactly one argument, a prefix to be applied to the pool and key names"
        exit 1
    fi
    
    PREFIX=$1
    
    vsm_cli cd10k create-pool -d performance -r 3 -n 4096 $PREFIX"-cinder-volumes"
    vsm_cli cd10k create-pool -d performance -r 3 -n 2048 $PREFIX"-ephemeral-vms"
    vsm_cli cd10k create-pool -d performance -r 3 -n 1024 $PREFIX"-glance-images"
    
    echo "Pools created"
    echo "Cinder volumes are in:     "$PREFIX"-cinder-volumes"
    echo "Nova ephemeral VMs are in: "$PREFIX"-ephemeral-vms"
    echo "Glance images are in:      "$PREFIX"-glance-images"
```    
I'm going to make there be just a single openstack key---easier, I think
```    
    ceph auth get-or-create "client."$PREFIX"-openstack" mon 'allow r' osd 'allow class-read object_prefix rbd_children, allow rwx pool='$PREFIX'-cinder-volumes, allow rwx pool='$PREFIX'-ephemeral-vms, allow rwx pool='$PREFIX'-glance-images' > "/etc/ceph/client."$PREFIX"-openstack.key"
    
    echo "Key created"
    echo "Key is available at:  /etc/ceph/client."$PREFIX"-openstack.key"
```
A corresponding script to delete the pools and key (it asks for confirmation several times):
```
    #!/bin/bash                                                                                               
    
    if [ $# -ne 1 ]; then
        echo "Please pass exactly one argument, a prefix to be applied to the pool and key names"
        exit 1
    fi
    
    PREFIX=$1
    
    vsm_cli cd10k delete-pool $PREFIX"-cinder-volumes"
    vsm_cli cd10k delete-pool $PREFIX"-ephemeral-vms"
    vsm_cli cd10k delete-pool $PREFIX"-glance-images"
    
    echo "Pools deleted"
    
    ceph auth del "client."$PREFIX"-openstack"
    rm "/etc/ceph/client."$PREFIX"-openstack.key"
    
    echo "Key deleted"
```

### 2015-8-03 kamfonik

Recording some info about the MOC-bot logging bot:
* [Documentation for Limnoria bot] (http://doc.supybot.aperio.fr/en/latest/).  Logging is handled by the ChannelLogger plugin but the bot has many other capabilities.
* Installed in the logger-bot VM on moc02, with static IP: `192.168.122.239` (I added this to /etc/hosts as `logger-bot`)
* User `moc` can log in to read copies of the log files in /home/moc/, which are synced every 10 minutes.  Password is `MOC-logs.4`
* User `moc-ops` has root access and control of the bot.  Various appropriate people have public key access to this account.  There is a short README file in the home directory.
* Myself, gsilvis, and jayh have a password for root access in the VM, but most bot tasks should be possible without it.

### 2015-6-24 kamfonik

Link to a [script for configuring the Cisco OBMs](https://github.com/CCI-MOC/moc/blob/master/scripts/cisco_obm_config.py).  Run it on your local machine with pexpect installed, make sure you have SSH access to whatever node you are using to get to the OBMs (e.g. the emergency-recovery node).  Configure it to your needs and then test on 1 server, because I'm sure there are plenty of new and exciting ways for it to break.  


### 2015-6-12 gsilvis

Here's a script to reboot all of Openstack.  I HAVE NOT YET RUN THIS, but I will update this when I do, and fix it if there are any mistakes:
```
    #!/bin/sh
    # Run this as root on the openstack controller.  You must have your own key
    # SSH-agent-forwarded.
    
    for i in {01,02,03,04,05,06,07,08,09,10,11}
    do
        ssh compute$i shutdown -r +2
    done
    
    shutdown -r now
```

### 2015-6-10 gsilvis: [updated 2015-6-23]

Got RHEL 7.1 booting.  Here's the set of things we have to do:
* remember to set /boot to ext3 [or similar]
* remember to make it NOT try to install grub.
* see https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/sect-kickstart-syntax.html
```    
    #   'bootloader --location=none' OR PERHAPS EVEN 'bootloader --extlinux' and skip the steps at the end?
    #   Needs more investigation
    
    subscription-manager register --username [email] --password [password]
    subscription-manager attach --auto
    
    yum install syslinux syslinux-extlinux
    
    mkdir /boot/syslinux
    cp -r /usr/share/syslinux/*.c32 /boot/syslinux/
    /sbin/extlinux --install /boot/syslinux
    dd bs=440 count=1 if=/usr/share/syslinux/mbr.bin of=/dev/md126
    
    cat > /boot/syslinux/syslinux.cfg << EOF
    DEFAULT rhel
    LABEL rhel
        LINUX ../vmlinuz-3.10.0-229.el7.x86_64
        APPEND root=$(blkid /dev/rhel/root | awk '{print $2}') rw
        INITRD ../initramfs-3.10.0-229.el7.x86_64.img
    EOF
```
Can't use RHEL 7.0, because it can't even make the needed partitions.  Can't use Centos 7.1, because it hasn't been released yet.  Can't use GRUB because it fails to install.

### 2015-4-27 gsilvis:

Okay, more Ceph problems.  In a desperate effort to get Ceph to be quiet, I decided to remove every possible way for Ceph to complain.  So, every OSD that was down, I removed with;
```
    ceph osd lost 10
    ceph osd rm 10
    ceph osd crush remove osd.10
```
Then, to make it as easy as possible for PGs to recover, I undid the change from yesterday:
```
    for i in {data,metadata,rbd,images,volumes,.rgw.root,.rgw.control,.rgw,.rgw.gc,.users.uid,.users.email,.users,.users.swift,.rgw.buckets.index,.rgw.buckets}; do ceph osd pool set $i size 3; done;
```

### 2015-4-24 gsilvis:

Some commands I ran on ceph:
```
    for i in {data,metadata,rbd,images,volumes,.rgw.root,.rgw.control,.rgw,.rgw.gc,.users.uid,.users.email,.users,.users.swift,.rgw.buckets.index,.rgw.buckets}; do ceph osd pool set $i min_size 1; done;
    for i in {data,metadata,rbd,images,volumes,.rgw.root,.rgw.control,.rgw,.rgw.gc,.users.uid,.users.email,.users,.users.swift,.rgw.buckets.index,.rgw.buckets}; do ceph osd pool set $i size 3; done;
```
Now, even 1 copy of an object is considered enough to reconstruct the object; and Ceph will hold 3 copies of each object.

On calamari:

    ceph-deploy --overwrite-conf osd create moc-ceph03:vde --zap

This makes a new Ceph OSD out of the listed drive, including partitioning it correctly.

### 2015-4-21 gsilvis:

On Harvard's advice, shut off vm-02 and reduced vm-03's ram to 2 GB instead of 8 GB.

### 2015-4-21 gsilvis:

When moc-sat is off, we have no DHCP on that network.  So, I switched moc-ssh-backdoor to a static address, 10.31.27.225 .  The only other machines that were using DHCP are moc-logstash, and perhaps moc-sensu.

### 2015-4-17 isd:

Two more disks died, and ceph choked. We've made a few modifications that should be recorded:

* set the minimum number of replications for healing to 1
* set the replics to 2 (permanently)

If `ceph health detail` reports inconsistent pages, you can fix them with `ceph pg repair`.

We've also shut down all non-mission-critical vms for memory usage.

### 2015-4-7 isd:

UPDATE: running puppet doesn't seem to disturb this change -- this makes sense, as this is how it is on the production controller as well.

The host networking on the development controller is working, but needs to be puppetized. Relative to what the puppet manifests are doing now, the following changes are needed:

in `/etc/sysconfig/network-scripts/ifcfg-em1.2444`, add:

```
VLAN=yes
```

in `/etc/sysconfig/network-scripts/ifcfg-br-ex`, add:

```
IPADDR=<desired public ip address>
NETMASK=255.255.255.0
VLAN=yes
```

and change `OVSBOOTPROTO` to `static`.

### 2015-3-6 isd:

We'd been having a strange issue where moc-sat wasn't remembering the host keys of machines it was logging in to via ssh. I traced this to an incorrectly set option in `/etc/ssh/ssh_config`:

    UserKnownHostsFile /dev/null

If I could think of a motive, I might be concerned about foul play, but I can't. I changed it to `~/.ssh/known_hosts`, and it should work going forward. We should keep an eye on this.

### 2015-2-25 gsilvis:

We had a problem where Cinder could not create volumes, because the volume daemon wasn't connecting to ceph.  It turns out that 'rbd_user' was changed from 'cinder' to 'volumes' in the last puppet update---reverting that fixed the problem.

One of the critical parts of narrowing down what was wrong was stracing the cinder-volume service.

    strace -f cinder-volume 2>&1 | tee /root/CINDERLOG

This outputs all system calls that ``cinder-volume`` makes (including all threads and child processes).  The general techniques used after that were:  grepping for things that should be happening, and tracing what specific process IDs were doing.  The first step:
```
    [root@controller root]# grep 'connect(' /root/CINDERLOG
    connect(4, {sa_family=AF_INET, sin_port=htons(80), sin_addr=inet_addr("8.8.8.8")}, 16) = 0
    read(7, "r__(self):\n        self.connect("..., 4096) = 4096
    [pid 46581] read(6, "r__(self):\n        self.connect("..., 4096) = 4096
    2015-02-25 11:55:10.984 46581 TRACE cinder.volume.drivers.rbd     client.connect()
    [pid 46581] connect(6, {sa_family=AF_LOCAL, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
    [pid 46581] connect(6, {sa_family=AF_LOCAL, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
    [pid 46581] connect(6, {sa_family=AF_INET, sin_port=htons(3306), sin_addr=inet_addr("10.31.27.207")}, 16) = 0
    [pid 46581] connect(7, {sa_family=AF_INET, sin_port=htons(5672), sin_addr=inet_addr("10.31.27.207")}, 16) = -1 EINPROGRESS (Operation now in progress)
    [pid 46581] connect(7, {sa_family=AF_INET, sin_port=htons(5672), sin_addr=inet_addr("10.31.27.207")}, 16) = 0
```
Note that no connections are made, except to 8.8.8.8 (DNS), and 10.31.27.207 (localhost)---this means that Cinder never even tries to connect to Ceph.  So, it's not a problem with the configuration of the Ceph cluster (though it could be a problem with Ceph configuration files on the Openstack controller), and it's not a networking problem.

The next step was to zoom in to the part of cinder-volume that should have been making a connection.  To do this, I added the line ``print "debug debug"`` to ``/usr/lib/python2.7/site-packages/rados.py``, at the beginning of the ``connect`` method.  Then, I searched the trace (using ``less``) for ``debug debug``, and looked what thread was outputting it.
```
    [root@controller gsilvis]# grep 'debug debug' /root/CINDERLOG.old -A 20
    [pid 46581] write(1, "debug debug\n", 12debug debug
    ) = 12
    [pid 46581] futex(0x37109a4, FUTEX_WAKE_OP_PRIVATE, 1, 1, 0x37109a0, {FUTEX_OP_SET, 0, FUTEX_OP_CMP_GT, 1}) = 1
    [pid 46584] <... futex resumed> )       = 0
    [pid 46584] futex(0x3710920, FUTEX_WAKE_PRIVATE, 1) = 0
    [pid 46584] futex(0x37109a4, FUTEX_WAIT_PRIVATE, 11, NULL <unfinished ...>
    [pid 46581] futex(0x37109a4, FUTEX_WAKE_OP_PRIVATE, 1, 1, 0x37109a0, {FUTEX_OP_SET, 0, FUTEX_OP_CMP_GT, 1} <unfinished ...>
    [pid 46584] <... futex resumed> )       = 0
    [pid 46581] <... futex resumed> )       = 1
    [pid 46584] futex(0x3710920, FUTEX_WAIT_PRIVATE, 2, NULL <unfinished ...>
    [pid 46581] futex(0x3710920, FUTEX_WAKE_PRIVATE, 1 <unfinished ...>
    [pid 46584] <... futex resumed> )       = -1 EAGAIN (Resource temporarily unavailable)
    [pid 46581] <... futex resumed> )       = 0
    [pid 46584] futex(0x3710920, FUTEX_WAKE_PRIVATE, 1) = 0
    [pid 46584] futex(0x37109a4, FUTEX_WAIT_PRIVATE, 13, NULL <unfinished ...>
    [pid 46581] rt_sigprocmask(SIG_BLOCK, ~[RTMIN RT_1], [], 8) = 0
    [pid 46581] mmap(NULL, 8392704, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS|MAP_STACK, -1, 0) = 0x7f05aed5f000
    [pid 46581] mprotect(0x7f05aed5f000, 4096, PROT_NONE) = 0
    [pid 46581] clone(child_stack=0x7f05af55efb0, flags=CLONE_VM|CLONE_FS|CLONE_FILES|CLONE_SIGHAND|CLONE_THREAD|CLONE_SYSVSEM|CLONE_SETTLS|CLONE_PARENT_SETTID|CLONE_CHILD_CLEARTID, parent_tidptr=0x7f05af55f9d0, tls=0x7f05af55f700, child_tidptr=0x7f05af55f9d0) = 46586
    Process 46586 attached
    [pid 46581] rt_sigprocmask(SIG_SETMASK, [],  <unfinished ...>
```
It isn't obvious how far to look here.  Let's try following just the thread that printed it, and removing all futex calls:
```
    [root@controller gsilvis]# grep 'debug debug' /root/CINDERLOG.old -A 1000 | grep -v futex | grep 46581 | head -n 25
    [pid 46581] write(1, "debug debug\n", 12debug debug
    [pid 46581] rt_sigprocmask(SIG_BLOCK, ~[RTMIN RT_1], [], 8) = 0
    [pid 46581] mmap(NULL, 8392704, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS|MAP_STACK, -1, 0) = 0x7f05aed5f000
    [pid 46581] mprotect(0x7f05aed5f000, 4096, PROT_NONE) = 0
    [pid 46581] clone(child_stack=0x7f05af55efb0, flags=CLONE_VM|CLONE_FS|CLONE_FILES|CLONE_SIGHAND|CLONE_THREAD|CLONE_SYSVSEM|CLONE_SETTLS|CLONE_PARENT_SETTID|CLONE_CHILD_CLEARTID, parent_tidptr=0x7f05af55f9d0, tls=0x7f05af55f700, child_tidptr=0x7f05af55f9d0) = 46586
    [pid 46581] rt_sigprocmask(SIG_SETMASK, [],  <unfinished ...>
    [pid 46581] <... rt_sigprocmask resumed> NULL, 8) = 0
    [pid 46581] rt_sigprocmask(SIG_BLOCK, ~[RTMIN RT_1],  <unfinished ...>
    [pid 46581] <... rt_sigprocmask resumed> [], 8) = 0
    [pid 46581] mmap(NULL, 8392704, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS|MAP_STACK, -1, 0) = 0x7f05ae55e000
    [pid 46581] mprotect(0x7f05ae55e000, 4096, PROT_NONE) = 0
    [pid 46581] clone(Process 46587 attached
    [pid 46581] <... clone resumed> child_stack=0x7f05aed5dfb0, flags=CLONE_VM|CLONE_FS|CLONE_FILES|CLONE_SIGHAND|CLONE_THREAD|CLONE_SYSVSEM|CLONE_SETTLS|CLONE_PARENT_SETTID|CLONE_CHILD_CLEARTID, parent_tidptr=0x7f05aed5e9d0, tls=0x7f05aed5e700, child_tidptr=0x7f05aed5e9d0) = 46587
    [pid 46581] rt_sigprocmask(SIG_SETMASK, [], NULL, 8) = 0
    [pid 46581] open("/etc/ceph/ceph.client.volumes.keyring", O_RDONLY <unfinished ...>
    [pid 46581] <... open resumed> )        = -1 ENOENT (No such file or directory)
    [pid 46581] open("/etc/ceph/ceph.keyring", O_RDONLY) = -1 ENOENT (No such file or directory)
    [pid 46581] open("/etc/ceph/keyring", O_RDONLY) = -1 ENOENT (No such file or directory)
    [pid 46581] open("/etc/ceph/keyring.bin", O_RDONLY) = -1 ENOENT (No such file or directory)
    [pid 46581] epoll_wait(5, {}, 1023, 0)  = 0
    [pid 46581] epoll_wait(5, {}, 1023, 0)  = 0
    [pid 46581] epoll_wait(5, {}, 1023, 0)  = 0
    [pid 46581] stat("/var/log/cinder/cinder-volume.log", {st_mode=S_IFREG|0644, st_size=49918, ...}) = 0
    [pid 46581] stat("/usr/lib/python2.7/site-packages/cinder/volume/drivers/rbd.py", {st_mode=S_IFREG|0644, st_size=33428, ...}) = 0
    [pid 46581] open("/usr/lib/python2.7/site-packages/cinder/volume/drivers/rbd.py", O_RDONLY) = 6
```
...and so forth.  Note the ``open`` calls---they are all to things in /etc/ceph that look like keyrings, and none of them existed.  If you look a little farther in this thread, you'll see that soon after it gives up and announces that it can't connect.  So, there's some sort of cinder/ceph misconfiguration.
```
    [root@controller gsilvis]# ls -lh /etc/ceph/
    total 24K
    -rw-------. 1 root root  63 Nov 20 18:21 ceph.client.admin.keyring
    -rw-r--r--. 1 root root 455 Nov 20 18:21 ceph.conf
    -rw-r--r--. 1 root root  64 Nov 20 18:21 client.cinder.key
    -rw-r--r--. 1 root root  64 Nov 20 18:21 client.glance.key
    -rw-r--r--. 1 root root  62 Nov 20 18:21 client.nova.key
    -rwxr-xr-x. 1 root root  92 Aug 25  2014 rbdmap
```
Note that there ARE keys there---they just have different names.  Also note that these files have not been modified in a long time, so they aren't the cause of the problem.  At this point, we looked at what puppet had changed in `/etc/cinder/cinder.conf`, and found the problem and fixed it.

### 2015-2-20 isd:

I reverted our mac address fix; this was causing problems with the networking on freshly provisioned compute nodes. I think the underlying problem is that we have the manifests configured to use em1 for networking, but they use p3p1 to boot, and we've only got the dhcp entry for the boot nic in satellite, so when it tries to dhcp on em1, it doesn't get a lease. If I'm right, there are a couple ways to solve this:

* Add another nic to the node on satellite, for em1. Have to be careful here though, it's easy to screw up the routing tables if you put two nics on the same subnet.
* configure the puppet manifests to use p3p1 for networking, instead of em1.

I didn't feel like messing with either of these just now, and all this means is we have to swap out that field when we re-provision.

### 2015-2-17 jbell:
Had to change ovs_tunnel_iface from eth1 to em1 in the top level manifests, nodes have em as nics. Not sure why it worked inside quickstack module

### 2015-2-13 isd:

I got the development environment on moc-sat working again. There are a lot of sensible ways to try to make a new environment that don't come out with something functional, here's a way that works:

1. Create a new environment on the satellite dashboard.
2. Go to the "Locations" tab for that environment, and make sure the default location has been added.
3. Manually create the directory `/etc/puppet/environments/${environment_name}` on moc-sat.
4. Populate the above directory with modules/manifests.
5. Go back to the dashboard and click on the import option. Satellite should detect the modules/manifests 
   that have been added. Approve the update.

We're backlogging a VLAN-isolated development environment for now; it wasn't as straightforward as I'd hoped and we've got other priorities.

### 2015-2-13 gsilvis:

Figured out why Horizon can use python-swiftclient while the 'swift' client can't.  The code with the faulty logic is in 'shell.py', which is not used by Horizon.  This, I suppose, shouldn't have been that surprising.

The bug in 'shell.py' was fixed about 6 months ago when they rewrote the file entirely.

### 2015-2-12 gsilvis:

Heat is fun!

![](_static/img/neutron_topology.png)

2015-2-11 gsilvis:

In the Rados Gateway's `/etc/ceph.conf`, set 

    rgw keystone accepted roles = admin _member_

This is needed for normal members to use Swift

### 2015-2-10 isd:

Stumbled across an option on satellite that may be the source of our mac-address reset problems:
```
    Administer -> Settings -> Provisioning -> ignore_puppet_facts_for_provisioning
```
The description of which is "Stop updating IP address and MAC values from Puppet facts."

I've flipped this to true -- hopefully this will solve the problem. Will update once we've had the chance to test.

UPDATE: This does in fact fix the problem; the mac address no longer resets after provisioning. Redhat is also reporting a bug for the underlying problem.

### 2015-2-10 jbell & isd:
Created new interface on moc-sat for dev network, assigned the ip address 192.168.42.1/24. When we tried to assign a 10 net address with /24 address space, it kept assigning a /8 address space... unsure why, but 192.168 worked, so we're sticking with that for the dev environment's network for the time being.

UPDATE: this turned out to be a simple typo -- We had `NEMASK=...` instead of `NETMASK=...`. Came across a message in the logs saying there was no netmask, and was just assuming `/24`. Presumably for the `10.` net it was just assuming `/8`. We've left it as a `192` subnet.

### 2015-2-6, jbell & isd:

We've discoverd a few more pieces of information about the foreman issues:

* changing the mac address works -- we were putting in the wrong mac address (pulled from the OS, which was doing *something* wrong); putting in the right one from the bios yields an error message about an existing lease, but clicking override seems to do the trick. 
* Afterwards, satellite changes the mac back to that of a different nic -- not the one it boots off of. We have to change it each time we want to provision a node.
* Hosts which are newly provisioned (as openstack compute nodes) are "in an error state." The following shows up at the end of the report:

```
err 	Puppet 	Could not retrieve catalog from remote server: Error 400 on SERVER: Local ip for ovs agent must be set when tunneling is enabled at /etc/puppet/environments/production/modules/neutron/manifests/agents/ovs.pp:32 on node compute12.moc.rc.fas.harvard.edu
notice 	Puppet 	Using cached catalog
err 	Puppet 	Could not retrieve catalog; skipping run
```

### 2015-2-5, jbell:
* Fixed the ntp zone config on compute03, was UTC. In order to fix, copy ETC file from `/usr/share/zoneinfo/ETC` to `/etc/localtime`. Should fix on machines that we are debugging on for correct time stamps, and look at how to puppetize time zone.

### 2015-2-5, gsilvis:

* We updated requests on the openstack controller using pip (pip install 'requests==1.2.1'), to make Ceph work correctly.
* We copied the Keystone admin token from /etc/keystone/keystone.conf on the controller to /etc/ceph/ceph.conf on moc-rgwb
* See issue #5

### 2015-01-09, pjd:
harvard cluster openstack setup networking problems

1) no DNS from guest. dnsmasq processes on the controller were not being given DNS servers to forward to, resulting in REJECT responses from guest DNS queries. Fixed this by changing /etc/neutron/dhcp_agent.ini on the controller:
```
    # Comma-separated list of DNS servers which will be used by dnsmasq
    # as forwarders.
    #dnsmasq_dns_servers = 10.31.27.201
    dnsmasq_dns_servers = 8.8.8.8
```
Note that the DNS server in resolv.conf on the controller (10.31.27.201) isn't reachable from the dnsmasq services.

2) connections to guest hang on large packets (makes it impossible to ssh from moc-ssh-gateway to guest floating IP) This is actually a driver issue with the ixgbe driver for the 10G NIC, and probably has something to do with mixing VLAN, VXLAN, untagged, and openvswitch.  Anyway, you can fix it by turning off large receive offload:
```
    [root@controller ~]# ethtool -K em1 lro off
    [root@controller ~]# ethtool -k em1 | grep offl
    tcp-segmentation-offload: on
    udp-fragmentation-offload: off [fixed]
    generic-segmentation-offload: on
    generic-receive-offload: on
    large-receive-offload: off
    rx-vlan-offload: on
    tx-vlan-offload: on
```
Red Hat support case 01326984 has been opened on this; hopefully we'll get informed when there's a proper fix to it. Note that neither of these two fixes have been integrated into the Puppet process for setting things up.

### 2014-12-22:

* ssh gateway and controller were unable to communicate. Adding `VLAN=yes` to `/etc/sysconfig/network-scripts/ifcfg-em1.2444` solves the issue.
* Confirmed by dcritch: We did in fact drop support for ssl with rabbit -- as such, the sed patch is not needed, and puppet agent *is* running on all the nodes.
* On moc-sat, all of `/etc/puppet/environments/production/modules/` is now versioned with git.
* The VNC consoles were not viewable except via the VPN, since the `vnchost_proxy` setting pointed at the controller's *private* IP address. Commit `569892732862bee8e463e085123fc3a08af70bea` to that repository solved the problem.

