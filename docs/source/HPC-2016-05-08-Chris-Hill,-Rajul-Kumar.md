###Discussed:###

* problems with setting-up dhcp server on a virtual subnet in openstack
	- no ip allocation to the a VM that is provisioned on the network with default dhcp disabled
	- dhcp server setup on a VM doesnt respond to the new VMs that come up on the same network for IP allocation
	- IP not allocated to the interface on force allocation using nova boot api. However the same IP is visible on opnestack dashboard. The VM responds over the network only when the same IP is manually allocated to the interface.

* live snapshot options 
	- this is to keep the states of the VM including the process/job states on the disk which could be used to restore the VM in the exact same state as it was before suspension. Snapshot a VM doesnt work in this case as it doesn't holds a process state and the jobs will restart when a VM is restored.
	- any method to do a snapshot so as to keep the states on the disk rather than memory
	- any way to release the resources when a VM is suspended so that the same resources can be used to provision a different set of VM to carry out the priority tasks. Once it's done, the suspended VM can be restored on those resources to continue with the job. Suspending a VM can help as it holds the process states as well.

* Chris spoke to Jason on live snapshot giving him an initial idea so that we could discuss further on what are the options and how could we go about it.

###To-Do:###
- find a workaround for dhcp
	- dhcp is being used to keep a IP-hostname mapping. find a way so that this could be done dynamically and /etc/hosts file for the dns server is updated so as to reach out the VMs using hostnames rather than IP
- find a way to release resources when a VM is suspended.

**Updates:**
- Rado suggested that DHCP server identifies a node only on its MAC address, which will change everytime you provision a VM. So using a DHCP server to do a static mapping doesn't sounds feasible. Though there is an option for hostname to IP mapping in dnsmaq.conf file, it was suggested to check how that actually works.
- Resource release is a feature that works at Hypervisor level. This is available with KVM. Piayani shared some docs. on how to suspend a VM and put its state on the disk using libvirt.