##Discussed##
- low priority vm migration across OpenStack nodes
	* An enhancement to current setup where a low priority suspended vm is migrated from one node to another if resources are available
	* Scenario: A low priority VM is suspended to do some high priority work.
		Now, on some other node, required resources free up for the VM. So low priority VM is live migrated from current node to the new node and is resumed there to carry out the execution.
- backfill concept
	* the jobs are queued up and an algo runs over it which checks for the duration of the jobs along with other specifications. Based on the findings, it queues up the low priority jobs in the slots available between high priority jobs which may be waiting for resources.
	* generally execution time of the job is specified. If not, alog assumes a default duration and location to execute the job
	* specifying the execution time betters the chances of execution quickly as it's easier for the algo to find the slots
	* job priority also changes with time based on specified factors like waiting duration etc.
	* backfill algo picks only a specific number of jobs to check rather than parsing through the whole queue so as to have a short return time.
- get some numbers on how this setup can help with job completion
	* can get some numbers from engage1 and OSG people with respect to the jobs, their execution time and how long they take to process
	* can also find the frequency of the jobs getting killed and how many times a specific job got killed before execution
	* can run some test to check how our setup can help to get better figures for execution
- paper on the task done till date
	* aggregate the findings, get the numbers and build an idea around whatever has been done till date to publish is somewhere.
	* will have to find the relevant locations to put this up
	* Chris will get in touch people around to find more about this


##To-do##
- Get done with the networking part allocated in the previous week using sshuttle. Cluster based on floating IP works.
- Work on the vm migration job as well. prefer doing it across nodes.
