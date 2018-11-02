###Discussed:###

* OpenStack environment: 
  - NFS, maintain state, etc using Salt. 
  - Everything to provision and deprovision nodes could be done in Salt. 
  - Idea of shared files:
    * May not be able to put hosts in shared directory.
    * Can put slurm.conf in shared directory
	* Different opinions on links to shared path to be a hard link or soft link. (May need to turn off SELinux to allow soft links.)
	  This is a test for after the special sever VM (see below) is made.
  - Going forward: put things (for OpenStack) into "Salt form". 
	* This can be done via separate Salt recipes. 
	* Figure out how to split up the salt scripts? Provide a list of separate scripts to be written?

* With salt, monitoring Python can still exist!
  - Salt scripts get called in place of provision/deprovision scripts. 

* Instead of a hosts file, run a DNS server. 
  - DNS server can run on same machine as salt-master. 
  - Uses "NameD" for DNS server, though DNSmasq may be easier. Hosts (compute nodes) check if server is up, dynamically get hostname -> IP mapping
	* There's a static file you put the DNSmasq server in. Find this out? 
	* Ask BMI group, etc, how do they handle this? Probably using DNSmasq. Map ip address to hostname. How does node know what IP, hostname to get?

* Remark on killing and resubmititng job: give users a choice.
  - find out if there's an sbatch/srun option for this?


###To-do:###
* Check on hard link vs soft link to be used to a path in shared in location
* Check with BMI team on how to use Dnsmasq for IP and hostname mapping with the node
* Explore the job resubmission options to without killing a job
* This week: Special VM with salt-master, DNSmasq, NFS master. Separate VM as gateway. 
* Provision/deprovision from gateway:
  - Using salt-cloud, bring up 5 VMs.
  - Using salt-server, configure VMs. 
* Put the configuration into Salt formula
* Down the line: Two different copies of this: one for engage1 work, one for BMI work. 
* Next week: Test slurm 16 (for Cloud features). 