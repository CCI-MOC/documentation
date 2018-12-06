FAQ for OpenStack instance e.g. why network on my instance does not work ?

*  Q:  I have launched an Ubuntu instance but I am not able to ssh into the system. I have tried on Windows and Ubuntu host OSs, but I am getting the "Connection Timed Out" error. 

   A: For accessing the instances from outside world, you need to make sure you have followed these steps:-
1. Your public ssh-key selected (in access and security section) while launching the instance so that you can use it to ssh to the instance.
2. Assign floating-ip to the instance in order to access it from outside world.
3. Make sure you have added rules in the security-groups to allow ssh to the instance.

A common issue is to not using a floating-ip to access the instance. You can associate a floating-ip to the instance by clicking "assign floating ip" and then clicking the "+" icon to allocate one if its not available.

Sample video for the same:-
https://www.youtube.com/watch?v=ZjdrVHPjltI

*  Q: Unable to perform "sudo yum update" on CentOS user i.e the "Colud not retrieve mirror list" error is thrown.

   A: This mostly because the DNS server is not mentioned under the Subnet details.
      * Go to Network -> Networks, Now click on the network that you setup while creating the instance. 
      * Clicking on the network name will take you to page titled "Network Overview"
      * On this page find the Subnets section and click on the "Edit Subnets" action button.
      * Click on the "Subnet Details" section of the dialog that opens.
      * Enter a DNS Name Server - 8.8.8.8
      * Save the changes.

#### Why do I see too much connection-drop errors? Is there a solution for it?
[Reason for connection drops and solution](https://github.com/CCI-MOC/moc-public/wiki/frequent-connection-drops-to-instances)


