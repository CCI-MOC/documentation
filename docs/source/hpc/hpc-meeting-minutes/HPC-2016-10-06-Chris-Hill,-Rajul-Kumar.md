Original minutes by Rajul, edited by Evan.

## Discussed:
* There are different physical (and virtual, in terms of network) zones within the MGHPCC. For ex, Engage1 is in the MIT pod, the MOC NEU cluster is in the NEU pod.
* The existing MIT cluster, Engage1, has:
 * 20 racks
 * Shared software pool
 * LDAP authentication
 * SLURM cluster
 * Salt provisioning
* Each zone has its own network. They are connected within MGHPCC by a "MeetMe switch". (Note: there is an image from Chris Hill that we can upload.)
* Engage1 backfills jobs from the Open Science Grid.
 * These jobs are virtualized, if I remember correctly. 
 * Could be a candidate for running on Kaizen.
* Machines in the NEU OpenStack environment could use Salt stack to configure the machines.
 * Existing nodes in Engage1 are configured with Salt and config recipes are in place/
* Configuring machines using Salt vs Pre-provisioned images - configurations changes frequently, easier to clone the recipes and run them or keep a check on config using Salt

## To-do: 
* Timeframe: Approximately one week.
* VM provisioning in HPC project on Kaizen with CentOS as compute node for SLURM cluster and RedHat for Salt master
 * Provision a vm with centos that can act as a SLURM compute node in future.
 * Federating to the existing engage1 cluster is a future job.
 * Could utilize anuj's scripts to create a cluster locally to check but there was no specific discussion on this.
 * Will try to configure the compute node with Salt recipe.
 * Salt master is just to run the scripts locally and understand how it works. <ay continue with a local master running the existing scripts cloned or federate with an existing master, but that is to be figured out later.
* Configure the VM using Salt recipes on local Salt stack 

## Next Step:
* Connect to existing Salt master or clone it on Kaizen
* Connect to the existing SLURM cluster
* Sharing the munge.key securely

## Future steps:
* Route the traffic between Kaizen and Engaging-1 over the internal network within MGHPCC
* Caching the shared software on kaizen from Engaging-1
* Carry out OSG jobs on virtual machines on kaizen
