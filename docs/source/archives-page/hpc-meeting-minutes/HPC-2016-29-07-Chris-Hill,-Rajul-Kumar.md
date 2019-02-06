###Discussed:###

* alternative approach to dhcp
   - have a script that takes dns server, slurm.conf file location, hostname and ip as an input and create a VM for the cluster 
   - have static files that stores the mapping of hostname to IP and checked everytime during VM creation, to pick a free name and IP for the VM. This gets updated with a status/flag to specify if a name/IP is available or not
	  * has to be single threaded to avoid concurrent updates and ambiguity

* update the config across the cluster for cluster update
   - restart cotroller is fine as long as the jobs in the queue doesnt gets killed
   - use slurm's restore config to update the config across the compute nodes

* suspend jobs and bring them back
   - hold the state of the vm during job suspend
     * suspend vs snapshot - suspend holds the process states in the memory for a specifc VM. However, snapshot keeps the state of the VM but doesnt holds the process state. Every process begings as a new one when restored
     * suspend vs pause vs shelve

* checkpointing - why it's not prefferd to restore the jobs 
   - it doesn't work with all the applications/jobs running on the cluster 
   - requires bookkeeping by end user.
   - requires user to design the application in such a way that it could be checkpointed. Adds an overhead
   - checkpointing doesn't support gpu related jobs

* target OSG jobs and setup as a basic use cases
   - add more features later for general use cases

###To-do:###

* setup a dhcp for virtual subnets on Openstack. If doesn't work as required then work on the alternative approach

* find the limitations of OpenStack wrt to resource management
  - check if the resources like CPU, memory etc. are released when a VM is suspended so that those resources can be allocated to other processes
  - ask OpenStack people here at MOC for this

* how to go about suspending the state of the VM including the process states
  - find out if there is something called live snapshot similar to live migration that stores the process states, in order to have it placed onto a disk and bring it back later