##Abstract for Poster##

Title: Expanding Traditional Computing Clusters with Dynamically Provisioned Virtual Nodes

Abstract: Traditional computing clusters are designed as static, bare metal installations, providing an essential optimized system for HPC workloads. However, these clusters are often tasked with supporting single node, single thread HTC workloads, which do not optimally utilize the specialized resources necessary for efficient multi-node jobs. In many cases, these backfill-style workloads could run equally well in virtual environments, as suggested, for example, by the success of the NSF's Open Science Grid and the ATLAS collaboration's distributed computing network.

We propose enabling the virtualization of appropriate workloads by expanding traditional bare-metal clusters with dynamically provisioned virtual nodes maintained under a single resource manager. We can utilize a single provisioning solution (eg, Salt) for both bare metal and virtual nodes. This provides access to a common file system, standard software, and job queueing system, simplifying the experience for the end user. The maintainer of the cluster can provision, checkpoint, migrate, and deprovision virtual resources as necessary and transparently to end user interaction. This model can also benefit the provider of the virtual environment by providing a steady supply of work, boosting utilization. 

We demonstrate an initial implementation of this model by expanding the engage1 Slurm cluster at MIT with virtual nodes out of the Massachusetts Open Cloud project's OpenStack deployment, Kaizen. 

##Discussed##

- HPC: tightly coupled, low latency jobs. Could run well virtualized as well. 

- Backfill idea is important. We're going to 
	- use virtualization to do backfill 
	- which is checkpointable 
	- migratable. 

	Note---there's a paper on High performance virtualization: MVAPICH, SR-IOV. Can also map GPUs into virtual environment. Chris will send. Need PCI Passthrough, whatever lets Infiniband and GPU be virtual. Keeping a cluster virtual -> maintainable in a virtual way without users suffering. Suspend, do work on physical machine, restore. 

- The selling point isn't virtualization. It's 
	- mobility, 
	- ability to checkpoint. 
	Doing this with OTC: any single threaded (or single node workload) will do. 

- "Tetris" analogy: easier to pack lots of little jobs in than big jobs. Single thread/node jobs are low hanging fruit, comparatively. 



- POSTER:

	-	Blocks for posters: five or six slides blocked together. (Ordered list below isn't very ordered.)

	1. Diagram: (General concept) sequence of events, low priority job, suspend when a high priority job comes up. when high priority job comes up, suspend low priority. Two columns/rows, arrows go across (two threads, contexts in time). 

	2. Concrete implementation: how we do it in Slurm, it's not an "immediate" context switch but it works, directions to work on. Is there a shortcut in slurm to improve latency? Slurm isn't designed for "microsecond" scheduling, it's for O(second) coarse grained scheduling. 

	3. Use cases: 
		* OSG
		* Checkpointing
		* "Backfill" style model
 
	4. Lessons learned section:
		* slurm.conf appears to cache IPs, need to flush when we bring VMs up or down.
		* need an NFS server on controller to keep new compute nodes synced, avoid timeout/drift errors.

	5. Where it's going: 
		* connecting virtual cluster to read cluster, a bit of a rabbit hole around networks and such (all solvable, but need to start figuring out what the issues even are). 
		* more autonomous: using live-migration type machinary, if low priority job suspends, high priority runs, but other resource becomes free, migrate low priority job and resume it---nice tool to have in our kit, can create a synthetic scenario, has three or four timelines, emphasize other timelines can be in other physical nodes (we did this in BMI for OpenStack cluster). 

		We have a mechanism on top of Slurm, now we're going to leverage it on top of OpenStack. 

	6. End message: All of this can happen without end users knowing anything. 

	7. Link to public git repo. 


- Findings from setting slurm cluster on OS
	- even though we put hostnames in the slurm.conf file, Slurm appears to cache IP addresses 
	- So we need to manually reload slurm.conf (slurmctrld reload), "flushes cache" mayhaps. 
	- Then need a sleep (15 sec or so) before resuming node. So add new node, update slurm.conf, daemon waits ~15 seconds, do scontrol to set new node to up. EVERYTHING goes through daemon which controls adding/removing nodes. 

	- Had to create an NTP server on controller, every compute node resyncs with controller to avoid auth timing issues. 



- OSG universe: you get access to all of these "free" resources, you just don't know when you'll have access, you'll get some of them some of the time. 


##To-do##
- Create VM running slurmd. Have a new VM in same OpenStack project, running in own network, but explore how to add it to first slurmd. Two ways: one with internal non-routable address, the other through public. This is a good test of what we need to do when we attach to the real cluster.
