# Architecture of Haadoop over Ceph Storage

<!--![](https://github.com/CCI-MOC/papers/blob/master/engage1/engage1_hadoop.png)-->

The CEPH storage has two tiers called cache tier and base tier.

  **Cache tier** has four OSD servers. Each OSD servers has 2 disks, and each disk has 1.5 TB SSD. 

  **Base tier** has ten OSD servers. Each Server has 9 Disks, and each disk has 4TB storage.

Cache tier has total 12TB storage, and base tier has total 360TB storage space.

We set up an isolated CEPH storage network and servers on each rack connected to CEPH storage servers through the Rados Gateway services (Rados gateways and Rados Gateway proxy)via 10G net- work.

As shown in the figure, two-tier architecture has four main steps for read operation:
1.  Hadoop scheduler asks Rados Gateway Proxy the location of data.
2.  Rados Gateway Proxy get location of data and re- turn the address of the closest Rados Gateway instance to the Hadoop Scheduler.
3.  The Hadoop scheduler assigns the job on the closest node to the Rados Gateway.
4.  The Rados Gateway tries to fetch the data from Cache Tier. If the block is already cached in the Rados Gateway then cache tier serves the request directly. If there’s a cache miss, then CEPH will automatically promote the object from Base Tier and then return the data back.

