
## Discussed ##

* Use Slurm 16:
	* The cluster on Engage1 is using slurm 16. So the new node will be consistent with the existing cluster.
	
* Get a block of IPs from Engage1:
	* these IP will be used by the virtual nodes as a part of the cluster.
	* Slurm.conf will have the nodes with the IPs from the block configured to federate the virtual nodes in the cluster.
	
* Setup a tunnel using sshuttle:
	* Create a gateway with the public IP which can be reached from the controller and create tunnels using sshuttle to bridge the networks

* Configure the virtual machine on Kaizen as compute node
	* Provision a VM and configure it with basic features using Slurm 16 so that it could be federated to the cluster.

* Already have a new slurm controller configured on Engage1 on a VM
	* Not being much used by others
	* Can use this to add this new node
	* It's on a VM. So can be replicated as well
	
* Open points:
	* Is there a dns server? If not how will the nodes be reached out dynamically in future
	* Specifc block IPs - how will this be mapped and kept consistent with the nodes and in slurm.conf
	* Ask slurm controller not to poll the nodes - polling the suspended nodes leads to down state of the node and the jobs are cleared off the queue
	* Salt master location - same as that of the cluster or use a local master with config syned from the main master or repo

* Check cvmfs:
	* It's a specific file system that contains packages used by OSG jobs 
	* Requires specifc config to reach out and mount the FS on the node
	* Check the link https://twiki.grid.iu.edu/bin/view/Documentation/Release3/InstallCvmfs to install the client on the node

## To-do ##

* Document the configs done so far:
	* Document the sshuttle config to create the gateway.
	* sshfs setup
* Upgrade the slurm node with slurm 16 and check the working