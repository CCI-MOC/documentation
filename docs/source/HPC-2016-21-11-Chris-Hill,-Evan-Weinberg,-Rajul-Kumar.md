##Discussed##

- setup the gateway using openvpn
	- will get the block of IP to be used as the subnet on virtual cluster
	- share the subnet on main cluster to be used as probable route
	- setup the gateway on main cluster using openvpn and public ip from kaizen
	- services like nfs on a node apart from controller on main cluster
	- check the reachibility
- shouldn't stop polling on main cluster
	- other nodes on the cluster might get affected
	- figure out an alternative using slurm reservation
- reservation to mark the node status as maint 
	- will not allocate the job when node is in maint status
	- node may not be marked as down when the VM is suspended
	- check the behaviour
	- can update the reservation duration as required to maintain the status
- cvmfs file system for osg jobs 
	- uses autofs
	- loads files on request 
	- requires to register the site and setup a proxy
	- try exposing it as sshfs/nfs
		- try sshfs on autofs and expose the sshfs mount point as nfs
	- check will work or not
- slurm nodes should know about other nodes across networks
	- tries to know the status of other nodes in the cluster
	- if not found then marks the node as down
	- will have to configure route for the node from main cluster to virtual cluster so as all the nodes to each other are visible

	
##To-Do##
- check the slurm behaviour anf job retention when a node is in maint state
- try exposing the cvmfs using sshfs/nfs and check the behaviour