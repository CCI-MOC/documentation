You need to deploy a ceph metadata server in order to use cephFS.  Details on this can be found in the ceph docs.

If you are not using CephFS, the metadata server will be on standby, like this:

    # ceph mds stat
    e26:, 1 up:standby

CephFS uses two pools, a data pool and a metadata pool:

     # ceph osd pool create <data_pool_name> <pg_num>
     # ceph osd pool create <metadata_pool_name> <pg_num>
     # ceph fs create <fs_name> <metadata_pool_name> <data_pool_name>

Now the metadata server should be active:

    # ceph mds stat
    e26: 1/1/1 up {0=ceph-vm=up:active}

In the above example ceph-vm is the hostname of the ceph server where the mds daemon is running, and so by default it is also the id of the metadata server.

On the CD10K, you only need to create the data pool, and use the command `vsm_cli <cluster_name> createfs <data_pool_name>`.  A metadata pool will be created automatically and named by prepending 'meta' to the data pool name:

     # vsm_cli cd10k create-pool -d performance -r 3 -n 512 atlas-data
     # vsm_cli cd10k createfs atlas-data
    
In the above example a metadata pool called metaatlas-data was created. 

### Cephx

Create a cephx keyring with access to the cephfs data pool:

     # ceph auth get-or-create client.cephfs-user osd 'allow rwx pool=<data_pool_name> mon 'allow r' 

### Mounting the filesystem

###### Kernel driver method

To find out if your kernel supports ceph, use `modinfo ceph`.  If not, you will need to use ceph-fuse (see below)

Get just the key string (the hash following `key=`) from your client.cephfs-user.keyring file, and paste just that into a file called 'cephfs-user.secret'.

If the client kernel supports ceph, you can mount it directly using 'mount'.

     # mkdir /mnt/cephfs
     # sudo mount -t ceph 192.168.28.11:6789:/ /tmp/cephfs -o name=cephfs-user,secretfile=/home/kamfonik/atlas.secret 

If `secretfile` doesn't work, try with the option `-o name=cephfs-user,secret=<your_key_string>`

You can unmount the file system like this: 

     # umount /tmp/cephfs

###### Ceph-fuse method

Install the ceph-fuse package on the client. This will create an /etc/ceph directory.

Put a copy of ceph.conf in /etc/ceph on the client with the `[global]` section configured appropriately.  You also need a copy of your cephx keyring on the client as well.  By default ceph-fuse will look for it in /etc/ceph.

Then mount the filesystem.  If you are not mounting as the cephx admin user, you need to specify the cephx user with `--id`:

     # mkdir /mnt/cephfs
     # ceph-fuse -m 192.168.28.11:6789 /mnt/cephfs --id cephfs-user

To specify a keyring file that is not in /etc/ceph, use `-k`:
  
     # ceph-fuse -m 192.168.28.11:6789 /mn/cephfs --id cephfs-user -k /path/to/keyring/file

To unmount the filesystem:

     # fusermount -u /mnt/cephfs


### Deleting CephFS and its pools

Getting rid of CephFS is tricky and not well documented.

**WARNING! The following steps will destroy any remaining data in the cephFS pools so make sure you really want to do this!**

You need to stop the mds service before removing the file system.  This might cause the cluster to go into a HEALTH_WARN state.  To stop the mds you need to know its id.  You can get this from `ceph mds stat` as explained above, or look at the ceph-mds process:

    # ps -ef | grep ceph-mds
    ceph      3065     1  0 15:38 ?        00:00:00 /usr/bin/ceph-mds -f --cluster ceph --id ceph-vm --setuser ceph --setgroup ceph

The `--id` flag is the interesting one.  In this example we see that our mds is called `ceph-vm` so we can stop the service like this:

    # systemctl stop ceph-mds@ceph-vm

Now you should be able to remove the filesystem:

    # ceph fs rm <fs_name> --yes-i-really-mean-it

And delete the pools:

    # ceph osd pool delete <metadata_pool_name> <metadata_pool_name> --yes-i-really-really-mean-it
    # ceph osd pool delete <data_pool_name> <data_pool_name> --yes-i-really-really-mean-it

You can start the mds service agian like this if needed:

    # systemctl start ceph-mds.target

The CD10K doesn't have a vsm_cli command to remove the cephfs.  If you want to just point it at new pools, you can do the `createfs` step to point it at the new pools, and after that you will be able to delete the old ones.  I have not completely tested the steps above on the CD10K, but in theory they should work, except the command to delete pools is:

    # vsm_cli cd10k delete-pool <pool_name>

### Troubleshooting

###### mount error 5 = Input/Output Error
In my test VM setup I found that if firewalld was running, the client would time out with `mount error 5`  when trying to mount, even if port 6789 was opened in the firewall.  Turning off firewalld completely solved this problem.

###### kernel does not support secretfile option
In one of my test cases the 'secretfile' option was not supported.  The error message was not very specific but would recommend to look at `dmesg | tail`.  There would be an error there: `libceph: bad option at secretfile=cephfs-user.secret`.

In this case you need to specify the cephx key on the command line with secret=<key> instead of using a file.

###### monclient hunting
You may get this cephx error if you do not specify --id and/or -k options with ceph-fuse.  Some instructions/documentation tell you that it's OK to  specify the client and keyring path in ceph.conf but this did not work for me.

### Useful link dump

**Note: a lot of these links recommend `ceph mds fail 0` to stop the mds server - but it doesn't work, another one just starts in its place.  Use the systemctl method above instead **

[Official CephFS docs](http://docs.ceph.com/docs/master/cephfs/)   
[ceph fs commands](http://docs.ceph.com/docs/master/cephfs/administration/)   
[ceph mds commands](http://docs.ceph.com/docs/master/man/8/ceph/#mds)   

[getting cephx to work](http://lists.ceph.com/pipermail/ceph-users-ceph.com/2014-April/038477.html)   
[removing cephfs](http://www.spinics.net/lists/ceph-users/msg17960.html)   
[deleting cephfs pools](http://lists.ceph.com/pipermail/ceph-users-ceph.com/2014-May/039762.html)   

