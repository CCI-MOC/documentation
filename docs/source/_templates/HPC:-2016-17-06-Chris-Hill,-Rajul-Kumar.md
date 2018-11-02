##Discussed:
- Salt cloud - usage and how it solves our purpose.  
 * a Saltstack offering that can connect with a cloud service provider like OpenStack, AWS etc.
        - provision the VM   
        - configure them for Salt    
        - federate it to the salt cluster
 * Post this, the VM is ready for configuration using salt recipes 
 * no explicit steps are required for setting up the VM to run salt recipes
 * Chris asked the system admins there for their reviews on salt-cloud. However, no reviews were available at that point.
- SSH issue while using salt cloud and its resolution   
 * check imposed on the number of SSH between a VM pair over internal network. 
 * blocked the deploy script run by salt-cloud for VM configuration as there were multiple SSH involved in it. 

 **Update**
 Rado/Rahul fixed this by disabling the check on internal network.

- Salt installation for master and minions(slaves) and its working    
  * Installing Saltstack and configuring master and minions to form a cluster
  * running the recipes to configure the nodes.    

  Refer [wiki](https://github.com/CCI-MOC/moc/wiki/Salt) on this.
- Salt cloud config files   
 Salt-cloud requires a set of config files for 
 * providers 
 * VM profiles 
 * VM instance specific configurations 
 required during the provisioning of VMs. [Salt wiki](https://github.com/CCI-MOC/moc/wiki/Salt#salt-cloud) contains more  details on this.
- Config recipes   
some existing recipes used on Engaging-1 cluster to configure nodes for Slurm and OSG containing the packages to be installed, user, groups, paths and soft links to be created 
- mghpcc repo - provided access to Chris' mghpcc git repository for reference

##To-do:
- Explore salt-cloud and provision VMs once the issue with SSH blocking is resolved.  
provision VM using salt cloud and check if they are configured and federated with salt-master
- Run config recipes, with required modifications, on a VM for Slurm
and OSG compute nodes provisioned using salt-cloud on OpenStack