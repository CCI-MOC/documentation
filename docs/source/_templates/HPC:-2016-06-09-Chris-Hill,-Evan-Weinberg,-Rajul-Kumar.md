##Discussed##

- experiment to get a new node with the same name in the slurm cluster
	- imposter the already existing node that was suspended for a higher priorty jobs
	- might be able to fake the node in the cluster so that slurm doesn't panic
	- might not be required if the status of the node doesn't change
	
- slurm controller could be restricted to check the status of the node by changing the slurmdTimeout parameter under timers in slurm.conf
	- applies to whole cluster
	- won't be able to know if a node actually failed
	- will have to put some external process to check the states of the nodes apart from slurm
	
- figure out a way to update the slurm not to check specific nodes for the status update
	- somehow modify slurm controller so that it doesn't check the status of the nodes that have been suspended
	- this will help keeping the nodes changing to down state and not loosing the jobs
	
- extended goal: migrate the suspended VM to a different physical node if the resources are available on it using live migration
	- a VM is suspended to provide resources to another VM for higher priority task. Now the resources are available on some other physical node in the same cluster to run that VM. So figure out if this VM could be moved to other physical node and run on that.
	
- differences in system clock on the VM that was suspended with respect to controller(VM running slurm controller)
	- update the clock or sync with ntp server
	- might create issues for the time dependent jobs
	
##To-do##

- run experiments to find the slurm behaviour when the slurmdTimeout is set to 0 and jobs are not lost in the process of restoring the nodes