##Discussed##

- Slurm cluster setup:
	- Salt-cloud: all cfg files for salt-cloud to create a VM. Connects to openstack, provisions VMs for you. 
		- cloud.maps.d: Need to hard code VM username/password, can't prompt you?
		- minion == salt slave. 
	- slurm-cluster: 
		- Gives instructions for creating salt-master, salt-cloud, then creating a VM for the SLURM controller. 
		- Every compute, controller name needs to start with a specific name (this is how the scripts are written). 
		- state.highstate -> points at just what's specified at top.sls. 
		- And, I have a salt-master (VM), a slurm-controller (VM), and compute notes (N, based on names of nodes under "provision and configure slurm compute nodes", ex. compute-1,compute-2, ...). Can also use the map file specified under salt-cloud. 
		- hosts file apart from /etc/hosts (used by dnsmasq) live in shared directory on controller. 

	- file-append in Salt state means append if entry doesn't already exist in the file

- If a host restarts, it gets a different hostname (novahost appended to the end). Need to fix that. Appears to be an OpenStack specific quirk. 

- slurm.conf can have include files that contains the config which can be resued in other conf files avoiding redundant steps.

- Need to look into alternative to DOWN perhaps---when a slurm daemon that's "DOWN" starts talking to the controller, it'll go to IDLE -> UP.

- Update readme for the slurm cluster setup. Add instructions on how to add munge.key, etc---make any and all commands explicit. 

- Use case for the setup and scripts
	- Can use this to create resources for a SLURM cluster.
	- Comment on features missing in OpenStack---suspend VMs in a resource-effective way. 
 
- Taking a node down in the cluster: 
	- set it to DRAINED, once the job is done it changes to IDLE+DRAINED, then kill the node. 
	- have a 'cron' job running on the salt-master, looks for IDLE nodes, whisks them away. 
 
- sshuttle -- wrapper for some python that puts in a bunch of tunneling-related stuff. Could use this to route traffic between NEU network and MIT network to attach engage1 to NEU openstack.
	- Need one public IP. Set up an sshuttle command, specifies that if you try to ping some block, actually ping over the network to some remote shuttled setup. Test pinging a machine in engage1 from Kaizen.
 
- HPC workload---single node jobs from Chris. Find out what exactly the NSDI people need, send e-mail to Chris. 
 
##To-do## 
* Work for next week:
	* Tidy up scripts to /remove/ compute nodes as well. Top priority!
	* Patch up holes in documentation.
	* Start to make a story around (temp fake workloads) having two nodes, fake sharing same resource---one running, switch to the other one. Workflow: SLURM is running, job computing low-priority 'A', someone else submits high priority workload 'B', instead of Slurm style premption, be able to suspend low priority workload, start high priority. Do this with two separate partitions, one has compute-1 (low priority) and other has compute-2 (high-priority). "Hand of god" looks in, sees compute-2 is down (suspended in OpenStack-y way). Reach in, suspend compute-1 in an OpenStack-y way (but not have SLURM panic about it), let compute-2 run.