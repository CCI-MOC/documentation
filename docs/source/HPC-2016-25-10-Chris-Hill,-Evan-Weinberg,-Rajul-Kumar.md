 ##Discussed##
 * BOSCO: plugin that takes jobs from OSG queue, puts into Slurm cluster, juggles the two, etc. 
 * API for OpenStack migration
	Check migration and live-migration apis from OpenStack to fulfill the purpose of a low prioirty VM migration.
 * Build a story: 
	* backfill job on node A, high priority job on B, new job comes along, throws out A, then B finishes.
	* so migrate backfill job to A and start it up on a differnet physical node.
 * Network piece: connecting Kaizen to MIT. 
	* We just have to work through it, do all the necessary routing. 
	* Over tunnels is fine, just have to make it work.
	* We're not as concerned about security, this is a proof of concept demonstration. 
 * Can do: 
	* create two tunnels, network across (which scales to a tunnel from every node to every node...). 
	* Not very practical. Create a single tunnel with internal routing. 
	* Using multiple tunnel method, we can "say" we added a virtual node to a real cluster. 
		* Method: slurm controller needs a tunnel which connects it to virtual node.
		* Also need a tunnel to connect virtual node to salt master. 
 * Should make a list of steps to try to connect virtual node to main cluster. 
	* What resources do we need to test this on engage1?
		* Physical controller on engage1
		* Salt master (which could be on Kaizen)
		* virtual node on Kaizen
		* Would need two way tunnel between controller and virtual node.
 
 * Steps:
	* Provision a node
	* Create tunnels (both on MIT end and NEU end, controller -> node and vice versa)
	* MIT shares munge key
	* MIT edit slurm.conf
	* Other files can be shared by "FUSE": https://github.com/libfuse/libfuse
	* This gets us the majority of the way there!