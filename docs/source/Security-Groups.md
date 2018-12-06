Before you launch a VM, you must configure some security settings so that you will be able to log in via ssh.

Navigate to Project -> Compute -> Access and Security
Click on the Security Groups tab.  
You should see a ‘default’ security group.  

<img src=http://i.imgur.com/difGeQ6.png>  

Security groups are profiles that control the firewall settings which allow or block access to instances that are members of the group.  
 
The default security group allows traffic only between members of the security group, so by default you can always connect between VMs in this group.  However, it blocks all traffic from outside, including incoming SSH connections.  In order to access instances via a public IP, an additional security group is needed.

Security groups are very highly configurable, so you can create different security groups for different types of VMs used in your project.  For example, for a VM that hosts a web page, you need a security group which allows access to ports 80 and 443.  You can also limit access based on where the traffic originates, using either IP addresses or security groups to define the allowed sources.

#### Create a new Security Group
Click on "Create Security Group."  Give your new group a name, and a brief description.  In the example, we will enable SSH from anywhere so we can access the VM via a public IP.

[[tutorial_screenshots/kilo/create_security_group.png]]

Your new group now appears in the list.  Click the "Manage Rules" button in the "Actions" column next to your new security group.

[[tutorial_screenshots/kilo/access_security_newgroup.png]]

You will see some existing rules:

[[tutorial_screenshots/kilo/security_rules_01.png]]
<!--
<img src=http://i.imgur.com/JzHIUbD.png>      
-->

Let's create the new rule to allow SSH. Click on Add Rule.

You will see there are a lot of options you can configure on this screen.  There is a built-in SSH option to make things simple.  Choose 'SSH' from the 'Rule' drop down menu and click "Add."  

[[tutorial_screenshots/kilo/add_rule.png]]

The new rule now appears in the list.

[[tutorial_screenshots/kilo/security_rules_02.png]]

##### Allowing Ping

The default configuration blocks ping responses, so you will need to add an additional group and/or rule if you want your public IPs to respond to ping requests. 

Ping is ICMP traffic, so the easiest way to allow it is to add a new rule and choose "ALL ICMP" from the dropdown.

[[tutorial_screenshots/mitaka/security_add_rule_ping.png]]

<!--
REMOVED THIS INSTRUCTION DUE TO KNOWN BUG
https://bugs.launchpad.net/horizon/+bug/1511748
which is not yet fixed in RHEL OSP Mitaka
If needed, you can also enable just ping, while still disallowing other ICMP traffic.  Choose the Type "Custom ICMP", and set Type=0, Code=8.
-->

More on different types of ICMP codes can be found at [this link](http://www.nthelp.com/icmp.html).  Note that due to a known bug (see https://bugs.launchpad.net/horizon/+bug/1511748) setting a specific ICMP type may give an error message.

Make sure to add VMs with public IPs you want to ping to the security group containing this rule.

<!-- 
#### Add a Rule to allow incoming ssh connections(22/tcp) 

Choose the Port Number as 22 

<img src=http://i.imgur.com/WnRhAy9.png>  
-->
***
 
#### Next:  [[Create a Key Pair]]  
###### Previous:  [[Create a Router]]  
[[Openstack Tutorial Index]]  