##Discussed:##
 - Remark on Salt: documentation is rather dispersed. 
   * May be attached to University of Utah. 
   * Seems to be a small group compared to Puppet, Chef. 
   * Should we switch to one of those two? (Namely Puppet, since the MOC uses it so much.)
 
 - Looks like we're close to running a job from engage1 cluster on Virtual node.
   * To do: Need compute node to be able to access controller. 
   * Need to securely transfer Munge key. 
   * VM needs public IP. 
   * Need to do various security setups. 
   * There are various paths in the slurm.conf file---ideally would be network synced, perhaps, but we can just "put them in place" on the compute node. 
      - Prolog files, state save location
 
 - Build up to tying to Chris' cluster:
   * Remark: Need a stable "snapshot" of these scripts on git. 
     - Inside "slurm" directory, create "anuj_centos7" directory with Anuj's scripts.
     - Create separate directory, "rajul_centos6" with current snapshot.
     - Third directory, "towards_engage1_centos6" with new work. 
   * Start adding features to our "fake" controller that asymptotically approaches Chris' controller (namely features in the slurm.conf file). 
   * Make sure it works at each step. 
 
##OSG:##
**Idea:**
 - Two node job in VMs, suspend VMs in an orchestrated fashion, bring them back up.
 - Basic idea of suspending a job that's running, using resources for something else, then paging it back in (all while keeping SLURM happy).
 - Let's say there's a VM running on a SLURM node. Suspend VM, put SLURM node in "Power Saving" mode to keep it from panicing?
 
##To-do:##
 - Setup a Slurm Controller more like engage1
    * What do we need to do on the real cluster? (Exchange e-mails with Chris)
 - Make a list of rsources to be shared; files, ports,munge key.
 - Homework thinking about suspend business in SLURM, how it interacts, who's asleep and who's dead.
 - Keep scripts on github
    * Put slurm.conf on github, but first we need to sanitize it for private information since it's based on Chris Hill's slurm scripts. 