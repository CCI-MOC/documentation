## Background
By attaching a Floating IP to your instance, you can ssh into your vm from your local machine.

Floating IPs are a limited resource, so your project will have a quota based on its needs.  You should only assign public IPs to VMS that need them.  

To get access to your other VMs, ssh from outside to a VM with a floating IP, then ssh over your private network.  (Make sure you are using key forwarding as described in [[Create a Key Pair]].)

## Allocate a Floating IP
Navigate to Project -> Compute -> Instances

Next to Instance Name -> Click Actions dropdown arrow (far right) -> Choose Associate Floating IP

[[tutorial_screenshots/kilo/floatingip_associate.png]]

If you have some floating IPs already allocated to your project which are not yet associated with a VM, they will be available in the dropdown list on this screen.

If you have no floating IPs allocated, or all your allocated IPs are in use already, the dropdown list will be empty.

[[tutorial_screenshots/kilo/floatingip_none.png]]

Click the + symbol to allocate an IP.  You will see the following screen.  Make sure 'public' appears in the dropdown menu, and that you have not already met your quota of allocated IPs.  In the example, the project has a quota of 2 floating IPs, but we have not allocated any yet, so we can allocate up to 2 IPs.

Click Allocate.

[[tutorial_screenshots/kilo/floatingip_allocate.png]]

You will get a green "success" popup in the top left that shows your public IP address.  You will get a red error message instead if you attempt to exceed your project's floating IP quota.  (If you have not tried to exceed your quota, but you get a red error message anyway, please contact moc-kaizen-l@bu.edu for help.)

[[tutorial_screenshots/kilo/floatingip_success.png]]

You can now see the floating IP is attached to your VM on the Instances page:

[[tutorial_screenshots/kilo/floatingip_is_associated.png]]

## Disassociate a Floating IP 

You may need to disassociate a Floating IP from an instance which no longer needs it, so you can assign it to one that does.

Navigate to Project -> Compute -> Instances

Find the instance you want to remove the IP from in the list.  Click the red "Disassociate Floating IP" to the right.

This IP will be disassociated from the instance, but it will still remain allocated to your project.

[[tutorial_screenshots/kilo/floatingip_disassociate.png]]

## Release a Floating IP

You may discover that your project does not need all the floating IPs that are allocated to it - perhaps at one time you used two, but now you only need one.

If this is the case, you should release the floating IP back to the pool so that other people can use it.  

Navigate to Project -> Compute -> Access and Security -> Floating IPs

Click the dropdown menu under the "Actions" column next to the IP you are not using, and choose "Release Floating IP".

The floating IP will be removed from your project. 

* **Important:** Don't release the floating IP if you know in the future you will need the exact same floating IP again.  Just keep the IP allocated to your project for future use.

[[tutorial_screenshots/kilo/floatingip_release.png]]

***

#### Next:  [[SSH to Cloud VM]]  
###### Previous:  [[Launch a VM]]  
[[Openstack Tutorial Index]] 
