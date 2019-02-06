###Discussed###

* methods to save running state of a VM and using the resources allocated to that VM to spawn a new VM
	- suspend the VM - on suspending a VM, the state of the VM is stored on the disk and resources are released to the free pool to be used for other purpose.
	So created the VM on OpenStack till all the resources were exhausted. Then suspended a VM and tried to create a new one. However, OpenStack didn't allowd to do this as it keeps the count of the resources consumed till that point even when a VM is suspended and the resources were released at hypervisor level.
	
	- attach a volume and boot from volume so that the state stays with the volume, which can be used later on to restore a VM.
	Created a volume on OpenStack with centOS image to boot from. Then booted a VM from the volume and suspended it. The state was kept on the volume as on resume the processes statrted from where they were before suspend. However, when terminated the VM and created another with the same volume, the state was not restored.
	
* prefer keeping the VM in a suspended state than creating other VM as parameters like IP, mac etc. will change for the new VM which may not allow to restore the state of the VM in a desired manner.
	

###To-Do###
* Speak to Jason on the possible ways to achieve this 
* Document the methods tried in order to achieve the desired result to save the state and release the resources for priority tasks