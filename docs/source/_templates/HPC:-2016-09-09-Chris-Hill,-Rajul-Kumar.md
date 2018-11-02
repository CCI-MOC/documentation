##Discussed##

- Develop a pipeline to show resource sharing(assumed) between two VM with different priorities
	- develop a pipeline as per the figure where two VMs with different priority exibit the use of same resources.
	- Initially VM1 with low priority runs some hpc jobs. When a request for some high priority job comes, VM1 is suspended along with the jobs and VM2 is provisioned, assuming they use the same resources. Once the job on VM2 completes, its deprovisioned and VM1 resumes executing the jobs.

- osgi
	- osgi jobs running on single node could be one of the use cases for this setup going forward 

- network across cluster
	- this can be achieved by doing some ssh tunneling but will look for better prospects at network level rather doing it in a upper layer and queuing all the jobs through a single point

- hybrid cluster with both bare-metal nodes and VM
	- a good experiment to test on a cluster with both bare-metal and VM as compute nodes
	- this is already being done but can test to see the behavior of the cluster

- backup slurm controller and slurmctld restart
	- generally we don't have a backup slurm controller in the cluster the cluster 
	- it's just used to keep the state of the nodes and the jobs. 
	- it doesn't affect the jobs that are running on the nodes. 
	- it's just that new jobs can't be queued on when a controller is down.  
	- however the restart time for the controller is very short. 
	- hence it's feasible to have just one controller that can be restarted when required.

- research paper - jitter in disk cause serious differece - related to work for NSDI
	- referred to a research paper that says even a small jitter in the disk can cause serious differnces in the latency

	
##To-do##

- Create the pipeline as mentioned and check how the cluster and jobs behave during the process