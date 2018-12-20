# Firmware Issue with Cisco 300GB Drive

* [Overview of the bug](#overview-of-the-bug)
* [Summary of Failure Scenario](#summary-of-failure-scenario)
* [Workaround](#workaround)
* [Walkthrough of the issue](#walkthrough-of-the-issue)
* [Useful links](#useful-links)

The stock disks on our Cisco UCS 220 servers (the disks themselves are SEAGATE ST300MM0006) have buggy firmware, and report a bogus parameter:

      [root@abc-moc01 ~]# cat /sys/dev/block/8:0/queue/optimal_io_size
      4294966784

This problem is explained briefly at [this RedHat Solution](https://access.redhat.com/solutions/519973) but the explanation is not very clear, and does not provide instructions on how to work around the problem other than upgrading the firmware.

******

### Overview of the bug
The value 4294966784 is in bytes, but parted works with this value in sectors.  Parted reports the value as a 'grainSize' of 8388607 sectors (since on this disk a sector is 512 bytes):

	[root@abc-moc01 ~]# python
	Python 2.6.6 (r266:84292, Feb 22 2013, 00:00:18) 
	[GCC 4.4.7 20120313 (Red Hat 4.4.7-3)] on linux2
	Type "help", "copyright", "credits" or "license" for more information.
	>>> import parted
	>>> sda=parted.Device(path="/dev/sda")
	>>> disk=parted.Disk(device=sda)
	>>> print sda.biosGeometry
	(36472, 255, 63)
        >>> print disk.partitionAlignment
        parted.Alignment instance --
          offset: 0  grainSize: 1
          PedAlignment: <_ped.Alignment object at 0x7f802bbeb290
        >>>> print sda.optimumAlignment
	parted.Alignment instance --
	  offset: 0  grainSize: 8388607
	  PedAlignment: <_ped.Alignment object at 0x7f802bbeb1d0>
	>>> print sda.minimumAlignment
	parted.Alignment instance --
	  offset: 0  grainSize: 1
	  PedAlignment: <_ped.Alignment object at 0x7f802bbeb290>
	>>> print sda.optimumAlignment.intersect(disk.partitionAlignment)
	parted.Alignment instance --
	  offset: 0  grainSize: 8388607
	  PedAlignment: <_ped.Alignment object at 0x7f802bbeb390>


This value, grainSize, is used by parted to align partitions for optimized performance:
* partition must be aligned to a sector that is a multiple of grainSize
* partitions cannot be smaller than grainSize
* grainSize amount of space is left for logical partition metadata

A typical value is something like 2048 or 4096 sectors (1 or 2 MB), so our >4GB value is absurd.  In the best case scenario, it causes parted to use up a lot of space in silly ways.  In the worst case, parted actually fails.

******

### Summary of Failure Scenario
When creating the first logical partition, Parted adjusts the starting sector by almost twice optimal_io_size, which leaves >8GB free space at the beginning of the extended partition.  

When creating a second logical partition, Parted will optimize by placing it in the smallest pocket of free space available.  

If the requested size of the second partition is less than 8GB, Parted tries to place it in the free space it left before the first partition, and then performs the same alignment adjustments that created the free space in the first place. 

This results in the start sector for the partition being outside the free space.  Anaconda catches this by comparing the start sector to the end sector, and throws an error.

******

### Workaround

The workaround is to do partitioning in the %pre section of the kickstart file, where you can specify start and end sectors. Below is an example that successfully creates Partition Table #1.

In this case the start and end sectors must be calculated by hand (or by script).  The following partition scheme uses 2048 sectors as an alignment value.

Here is the %pre section:

	%pre
	#clear the MBR and partition table
	dd if=/dev/zero of=/dev/sda bs=512 count=64
	parted -s /dev/sda mklabel msdos

	# Without the sleep commands, this  fails irregularly
	# The disk apparently needs time to settle

	parted -s /dev/sda mkpart primary ext4 2048s 526335s         # /boot
	sleep 10
	parted -s /dev/sda mkpart primary 526336s 34080767s          # swap
	sleep 10
	parted -s /dev/sda mkpart primary ext4 34080768s 50857983s   # /
	sleep 10
	parted -s /dev/sda mkpart extended 50857984s 100%            # [extended]
	sleep 10
	parted -s /dev/sda mkpart logical ext4 50860032s 59248639s   # /var
	sleep 10
	parted -s /dev/sda mkpart logical ext4 59250688s 63444991s   #/tmp
	sleep 10
	parted -s /dev/sda mkpart logical ext4 63447040s 67641343s   #/home
	sleep 10
	parted -s /dev/sda mkpart logical ext4 67643392s 100%        #/scratch
	sleep 10
	%end

And here's what goes in the normal kickstart partitioning section:

	clearpart --none
	part /boot      --onpart=/dev/sda1
	part swap       --onpart=/dev/sda2
	part /          --onpart=/dev/sda3
	part /var       --onpart=/dev/sda5
	part /tmp       --onpart=/dev/sda6
	part /home      --onpart=/dev/sda7
	part /scratch   --onpart=/dev/sda8

******

### Walkthrough of the Issue
Walking through the issue shows why parted fails sometimes, but not others.  Suppose we want to create the following partition table in a kickstart install:

	# Partition Table #1 - This one fails
	part /boot --asprimary --fstype="ext4"  --size=256
	part swap  --asprimary --fstype="swap"  --size=8192
	part /     --asprimary --fstype="ext4"  --size=16384
	part /var              --fstype="ext4"  --size=4096
	part /tmp              --fstype="ext4"  --size=2048
	part /home             --fstype="ext4"  --size-2048
	part /scratch          --fstype="ext4"  --grow --size=1

The above partition table will cause the installation to fail.  However, this reduced version will install:

	# Partition Table #2 - this version doesn't cause anaconda to fail
	part /boot --asprimary --fstype="ext4"  --size=256
	part swap  --asprimary --fstype="swap"  --size=8192
	part /     --asprimary --fstype="ext4"  --size=16384
	part /var              --fstype="ext4"  --size=4096

When we do an installation using Partition Table #2, here is what it actually does to the disk - note that the partition sizes are not what we asked for, and there are weird free space pockets:

	[root@abc-moc02 kamfonik]# parted /dev/sda print free
	Model: SEAGATE ST300MM0006 (scsi)
	Disk /dev/sda: 300GB
	Sector size (logical/physical): 512B/512B
	Partition Table: msdos

	Number  Start   End     Size    Type      File system     Flags
	        32.3kB  4295MB  4295MB            Free Space
	 1      4295MB  8590MB  4295MB  primary   ext4            boot
	 2      8590MB  30.1GB  21.5GB  primary   ext4
	 3      30.1GB  42.9GB  12.9GB  primary   linux-swap(v1)
	 4      42.9GB  296GB   253GB   extended
	        42.9GB  51.5GB  8582MB            Free Space
	 5      51.5GB  60.1GB  8590MB  logical   ext4
	        60.1GB  296GB   236GB             Free Space
	        296GB   300GB   3647MB            Free Space

Comparing anaconda.storage.logs for the the two attempts, with some looks into [anaconda's source code](https://github.com/abiquo/anaconda-ee-el6/blob/6542236e597473e7a06df6b234802f13f5982ca4/anaconda-ee-13.21.195/storage/partitioning.py) and , allows us to isolate why #1 fails.


### Walkthrough - Partition Table #2
Parted starts with the unpartitioned 300GB disk. The start sector is 63, which is normal for an msdos partition table.

However, 63 is not a multiple of 8388607. Parted adjusts the start sector to 8388607 (going forward, this value is referred to as 'grainSize').  This leaves 4295MB of free space at the start of the disk.

Our first partition is /boot, which we have specified at size 256MB, or 524288 sectors. (256*1024*1024/512 = 524288)

524288 isn't a multiple of grainSize, so parted increases the size until it is - which in this case means our /boot partition is now 4295MB instead of 256MB.

Our next partition, swap, is specified at 8192MB which is 16777216 sectors.  It is too big for the alignment boundary at `grainSize*2=16777214`, so the size is increased to `grainSize*3=25165821` sectors, or 12287MB.

Note that we've now used up about 20G of our disk when all we actually wanted was one 256MB partition and one 8192MB partition!

Same thing happens to the / partition, which we specified at 16384MB.  It gets adjusted to `grainSize*5=41943035` sectors or 20479MB.

So far, we only created logical partitions, and although we wasted a lot of space, the partitions did get created.

Now, we create the extended partition.  This mostly goes as it should, except that the end of the partition is brought back further than it normally would be from the end of the disk, due to the large number of sectors between alignment boundaries.

Next, we create the /var logical partition.  We have asked for 4096MB, which is 8388608 sectors, so we get `grainSize*2` sectors or 8590MB.

For logical partitions, parted tries to optimize by finding the smallest acceptable piece of free space it can use. 

The logs aren't easy to follow without examining the source code, but here's the summary: 
* It scans the free space regions, looking for the smallest acceptable one.  
* As each acceptable region is found, if it's smaller than the last acceptable one, it gets stored in a "best_free" variable.  
* Once it's done, it proceeds to create the partition in best_free.

In this case, it looks at 3 free space regions, checking to see whether they are inside the extended partition, and if they are large enough.

The 4295MB of space left the beginning of the disk due to the large grain size is pretty big, but it isn't in the extended partition, so it gets rejected:

     14:50:38,808 DEBUG   : looking for intersection between extended (83886070-578813882) and free (63-8388606)
     14:50:38,808 DEBUG   : free region not suitable for request

Next, the extended partition is checked, and found suitable, but there is still more to scan, so we just note it and keep going:

     14:50:38,808 DEBUG   : looking for intersection between extended (83886070-578813882) and free (83886133-578813882)
     14:50:38,808 DEBUG   : current free range is 83886133-578813882 (241663MB)

Parted also checks that bit of space we left at the end of the disk when creating the extended partition, but again, it doesn't overlap with the extended partition, so it's rejected:

     14:50:38,808 DEBUG   : looking for intersection between extended (83886070-578813882) and free (578813883-585937499)
     14:50:38,808 DEBUG   : free region not suitable for request

So, we go back to the acceptable partition we found before. But wait! It starts at 83886133, which is not a multiple of grainSize. 

Because it is a logical partition, it first gets bumped to the next multiple of grainSize, the grainSize is added again in order to leave room to store the logical partition metadata (see lines 785-793 at the [anaconda source code](https://github.com/abiquo/anaconda-ee-el6/blob/6542236e597473e7a06df6b234802f13f5982ca4/anaconda-ee-13.21.195/storage/partitioning.py) to understand how this happens)

     14:50:38,809 DEBUG   : adjusted start sector from 83886133 to 100663284
     14:50:38,809 DEBUG   : adjusted length from 8388608 to 16777214
     14:50:38,809 DEBUG   : created partition sda5 of 8191MB and added it to /dev/sda

This leaves 8590MB of empty space at the beginning of the extended partition.

Partition Table #2 is now finished, so the installation continues, but we get the actual partition table posted at the top of this page - full of overlarge partitions and weird free space pockets.

If you want to see a continuous version of the log file quoted above, it is pasted [below](#logs)

### Walkthrough - Partition Table #1

Partition Table #1 works exactly the same way as Partition Table #2, until it finishes creating the /var partition.  At that point, #2 is done, but #1 needs to create the /tmp partition.  That's when things fall apart.

We have specified /tmp at 2048MB, or 4194304 sectors.  This is smaller than grainSize.

Parted goes through the same optimization as before, scanning free spaces for the smallest suitable option.  It rejects the block at the very beginning of the disk (63-8388606) because it's not on the extended partition.  

But it *accepts* the 8590MB block at the beginning of the extended partition (83886133-578813882), which was created after the start sector for /var was adjusted by two grainSize boundaries:

	14:50:38,808 DEBUG   : looking for intersection between extended (83886070-578813882) and free (83886133-578813882)
	14:50:38,808 DEBUG   : current free range is 83886133-578813882 (241663MB)

This is reasonable behavior for Parted - of course it can fit a 2048MB partition in 8184MB of free space.  But Parted is not counting on grainSize being twice the size of the partition itself.

Once again, we adjust the start sector by two boundaries, once to align and once to leave metadata space.  

     18:17:02,374 DEBUG   : adjusted start sector from 83886133 to 100663284
     18:17:02,374 DEBUG   : adjusted length from 4194304 to -8388607
     18:17:02,374 DEBUG   : created partition sda5 of 8191MB and added it to /dev/sda

Notice that the partition has a negative length!  Luckily, this problem is caught in the code: 

    if not disklabel.endAlignment.isAligned(free, end):
        end = disklabel.endAlignment.alignNearest(free, end)
        log.debug("adjusted length from %d to %d" % (length, end - start + 1))
        if start > end:
            raise PartitioningError("unable to allocate aligned partition")


So to summarize, Parted tried to place /tmp within sectors 83886133-100647224, which were left free by the alignment adjustments done while creating /var.  

The same alignment adjustments are then performed for /tmp, so the starting sector of the new partition is bumped up to the next grainSize boundary, at 92274677.  

Parted adds grainSize again to store the logical partition metadata, and the starting point is now 100663284 - which isn't even inside the pocket of free space.

Meanwhile, Parted sets the end sector to 92274677, which satifies both these conditions:
* a multiple of grainSize 
* as close as possible to the end of the free space (100647224) while still being inside it

Our proposed partition is now defined with its start at 100663284, and its end at 92274677.  That's where the negative 'length' comes from.

Anaconda has a check built in for this (`if start > end:`), and that's when the installation throws the error and fails.

### Logs
Continuous version of the section of anaconda.storage.log quoted in the Partition #2 walkthrough:

	14:50:38,806 DEBUG   : new free: parted.Geometry instance --
	  start: grainSize  end: 585937499  length: 502051430
	  device: <parted.device.Device object at 0x2dcc150>  PedGeometry: <_ped.Geometry object at 0x2dcc750> (83886070-585937499 / 245142MB)
	14:50:38,806 DEBUG   : new free allows for 0 sectors of growth
	14:50:38,807 DEBUG   : creating extended partition
	14:50:38,807 DEBUG   : adjusted length from 502051430 to 494927813
	14:50:38,807 DEBUG   : recalculating free space
	14:50:38,807 DEBUG   : getBestFreeSpaceRegion: disk=/dev/sda part_type=1 req_size=4096MB boot=False best=None grow=False
	14:50:38,808 DEBUG   : looking for intersection between extended (83886070-578813882) and free (63-8388606)
	14:50:38,808 DEBUG   : free region not suitable for request
	14:50:38,808 DEBUG   : looking for intersection between extended (83886070-578813882) and free (83886133-578813882)
	14:50:38,808 DEBUG   : current free range is 83886133-578813882 (241663MB)
	14:50:38,808 DEBUG   : looking for intersection between extended (83886070-578813882) and free (578813883-585937499)
	14:50:38,808 DEBUG   : free region not suitable for request
	14:50:38,809 DEBUG   : adjusted start sector from 83886133 to 100663284
	14:50:38,809 DEBUG   : adjusted length from 8388608 to 16777214
	14:50:38,809 DEBUG   : created partition sda5 of 8191MB and added it to /dev/sda


Continuous version of Partition Table #1 Log:

	18:17:02,371 DEBUG   : getBestFreeSpaceRegion: disk=/dev/sda part_type=1 req_size=4096MB boot=False best=None grow=False
	18:17:02,372 DEBUG   : looking for intersection between extended (83886070-578813882) and free (63-8388606)
	18:17:02,372 DEBUG   : free region not suitable for request
	18:17:02,372 DEBUG   : looking for intersection between extended (83886070-578813882) and free (83886133-578813882)
	18:17:02,372 DEBUG   : current free range is 83886133-100647224 (8184MB)
	18:17:02,372 DEBUG   : looking for intersection between extended (83886070-578813882) and free (117440498-578813882)
	18:17:02,372 DEBUG   : current free range is 117440498-578813882 (225279MB)
         
	18:17:02,372 DEBUG   : looking for intersection between extended (83886070-578813882) and free (578813883-585937499)
	18:17:02,373 DEBUG   : free region not suitable for request

### Useful links
* [RedHat Solution noting the issue](https://access.redhat.com/solutions/519973)
* [listserv exchange with a helpful explanation of the issue](http://comments.gmane.org/gmane.linux.scsi/81164)
* [relevant Anaconda source code](https://github.com/abiquo/anaconda-ee-el6/blob/6542236e597473e7a06df6b234802f13f5982ca4/anaconda-ee-13.21.195/storage/partitioning.py)
* [relevant Parted source code](http://pyparted.sourcearchive.com/documentation/2.0.12-1/alignment_8py-source.html)

