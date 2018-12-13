**Discussed:**

* Running the VM compute nodes for HPC cluster
 - Short term: running on engage1 OpenStack (quicker, homogeneous tests)       
   Long term: also running on Kaizen OpenStack (politically looks good, cross campus lines connectivity and resource sharing)
 - Avoids the short term issues of the MeetMe switch, but there are still issues bridging networks within MIT. 
 - We should be able to do both, and they should work the same way (up to network cfg issues).
 - Make sure we get an account on Liberty OpenStack to verify the configuration to have a similar setup. (This is not a substitute!)

* Path to project goal:
  Make one of these VMs visible (by some route) to the main engage1 cluster and federate with existing slurm master to receive jobs.      
  Idea:
  - slurm.conf can just include an arbitrary public IP for a virtual node so that it's known and visible to the slurm master for the job assignment.
  - Can set up a private LDAP mirror for VM for security purposes.
    * one way mirror only for verification and no modification---can't mess up mirror
    * This syncs up users frm the master
  - OSG route: 
    * Current implementation: 
      - proxy host user ID in engage1. 
         * Simplifies user id story if we only target OSG first---only one user ID.
         * Takes work from Open Science workflow cloud, runs it on SLURM cluster. 
      - runs when there are idle cycles, can get killed.
      - Uses virtual file system.	
    * We could have it be the only user, work 24/7.  
    * Issue: OSG runs could get killed every 15 minutes, if they aren't checkpointing, they restart and make no progress.

      Soln: Runs in virtual machine, can checkpoint before they get killed.
    * OSG services can be suspended depending on if other work comes along. (Gives a way to use idle cycles)     
      Remember: Cloud, how do we support bursty needs, vs. steady scientific computing. 
    * May want to mirror their CVMFS world - gets us out of LDAP world, but there are security concerns about giving access to CVMFS. 

* Initial requirements for OSG route:
  - Need to get OSG account. 
  - CVMFS - namespace in the OSG, based on HTTP perhaps OR We could set up our own server to host our own stuff
  - End to end story: we're going to use OSG to support these end-to-end workflows, similar to what other people may do. 
  - One user id in our SLURM piece: OSG proxy. 


**To-do**

* Get salt-cloud work on github 

  Update:
  - config files and recipie for salt-cloud and salt updated on github
  - updated wiki with the steps for use
* Take cloud-stack stuff, be able to instantiate a flexible number of VMs. This is a reference set up anyone can "checkout and run."

  Update:
  - salt-cloud map config file can be used to specify the number of VMs to be provisioned with their specifc configuration profiles.
  - salt-cloud and salt-master installation and config files from git can be used to setup a salt VM cluster.