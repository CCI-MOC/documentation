# Accessing
To access: ssh user@192.168.122.80
* Password access disabled. Must have key in authorized_keys enabled
* VM is ceph-iscsi-gateway on neu-haas-master
* user's password is <default>

On the storage network, IP is 192.168.28.20
On the iSCSI haas network (project ceph-iscsi net iscsi-net): 192.168.29.20

# Nodes

cisco-07 is on the iscsi network, installed to ceph rbd 'ubuntu-1'

# Ceph

Ceph pool name: boot-disk-prototype

ceph config:
```
[global]
mon_initial_members = storage1,storage2,storage3
mon_host = 192.168.28.11:6789,192.168.28.12:6789,192.168.28.13:6789
auth_cluster_required = cephx
auth_service_required = cephx
auth_client_required = cephx

#This name has to be same as the keyring file
# Change this for your key
[client.henn]
keyring = /etc/ceph/client.henn.key
```
Commands:
```
rbd --keyring /etc/ceph/client.henn.key --id henn create --size 8192 --image-format 2 boot-disk-prototype/ubuntu-1
rbd --keyring /etc/ceph/client.henn.key --id henn ls boot-disk-prototype
rbd --keyring /etc/ceph/client.henn.key --id henn map boot-disk-prototype/ubuntu-1
```

## Making it easier

You can automate the `--keyring` and `-id` steps by appending this to .bashrc (this is already done on the ceph-iscsi-gateway):
```
export CEPH_ARGS="--id henn --pool boot-disk-prototype"
```


## Cloning

Take a look at the various rbds in our ceph pool:
```
user@ceph-iscsi-gateway:~$ rbd ls -l
NAME                                  SIZE PARENT                                 FMT PROT LOCK 
rand8G                               8192M                                          1           
test.img                            10240M                                          1           
ubuntu-1-backup                      8192M                                          1           
ubuntu-cloud                             0                                          1           
centos                               8192M                                          2           
centos@orig                          8192M                                          2 yes       
centos-working                      10240M                                          2           
centos-working@before-grub2-changes 10240M                                          2 yes       
ubuntu-1                             8192M                                          2           
ubuntu-1@installed                   8192M                                          2 yes       
ubuntu-2                             8192M boot-disk-prototype/ubuntu-1@installed   2           
```

It's important to note, in order to do efficient cloning (ie - thin copies), you have to have used ceph image-format 2. To check what format something is in:
```
$ rbd info test.img
rbd image 'test.img':
	size 10240 MB in 2560 objects
	order 22 (4096 kB objects)
	block_name_prefix: rb.0.43bf79.238e1f29
	format: 1

```


To convert it:
```
rbd export test.img - | rbd import --image-format 2 - test.img2
```

Now it looks like:
```
$ rbd info test.img2
rbd image 'test.img2':
	size 10240 MB in 2560 objects
	order 22 (4096 kB objects)
	block_name_prefix: rbd_data.50ad452ae8944a
	format: 2
	features: layering
	flags: object map invalid
```


```
user@ceph-iscsi-gateway:~$ rbd snap rm test.img@snapshot
user@ceph-iscsi-gateway:~$ rbd snap create test.img2@snapshot
user@ceph-iscsi-gateway:~$ rbd snap protect test.img2@snapshot
user@ceph-iscsi-gateway:~$ rbd clone test.img2@snapshot test.img3
rbd: clone error: (17) File exists2015-10-15 15:57:02.042329 7ff9ab53c7c0 -1 librbd: rbd image test.img3 already exists

user@ceph-iscsi-gateway:~$ rbd clone test.img2@snapshot boot-disk-prototype/test.img3
user@ceph-iscsi-gateway:~$ time rbd clone test.img2@snapshot boot-disk-prototype/test.img4
```

# Mounting the ceph node on the iSCSI gateway
To map the ceph rbd as a block device on the iSCSI gateway:
`rbd --keyring /etc/ceph/client.henn.key --id henn map boot-disk-prototype/ubuntu-1`

To mount it:
`mount -r /dev/rbd0p1 /mnt`

# iSCSI target server

Jason configured the iSCSI gateway to use iet. LUNs can be configured through `/etc/iet/ietd.conf`.

## Software
Jason used iet initially, but there are also tgt (full featured, user-mode
based one that has [specific ceph
support](http://ceph.com/dev-notes/updates-to-ceph-tgt-iscsi-support/)) and LIO
(rhel 7's new default; before it was tgt).

[This
page](http://linuksovi.blogspot.com/2014/09/red-hat-7-centos-7-iscsi-target-with-lio.html)
has a short comparison of tgt and LIO. It links to [an
email](http://sourceforge.net/p/scst/mailman/message/30688206/) from a random
person that looks unfavorably towards LIO and iet. Given that (assuming no one
has a strong opinion on this), it may be prudent to go with tgt.

# Resources for chainbooting iSCSI
* http://it-joe.com/howtos/iscsi.php
 * The end of CentOS Method 1 does what we want - boots from whatever happens to be on the iSCSI device
* http://www.held.org.il/blog/2010/10/booting-linux-from-iscsi/


Persisting block devices coming from ceph during reboot:

http://ceph.com/planet/mapunmap-rbd-device-on-bootshutdown/