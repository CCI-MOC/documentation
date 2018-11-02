# Useful links

- According to "devvesa" in #midonet IRC channel at 1:04 pm. on Aug. 6, 2015
 
    > devvesa: devstack works in our daily tests on stackforge gates for the neutron plugin

    > devvesa: https://review.openstack.org/#/q/status:open+project:openstack/networking-midonet,n,z

    > devvesa: click on any patch, and you will see a `gate-tempest-dsvm-networking-midonet`

    > devvesa: that's devstack + midonet + tempests tests

What devvesa meant was to click on "certain" patch such as https://review.openstack.org/#/c/207783/... 

# Midonet Deployment

>According to this [page](https://github.com/midonet/midostack), Midostack is not an option for us because it is deprecated in favor of Devstack 

>According to the instruction on this [page](https://support.software.dell.com/foglight-for-virtualization-enterprise-edition/kb/138380), the Current devstack version is kilo

>According to this [page](http://blog.midonet.org/midonet-2015-03-release/, Current midonet version is v2015.03

###Steps for install Midonet on kilo version Devstack
1. Go to https://horizon.csail.mit.edu and login through horizon dashboard

2. Before you create an instance, you should have your ssh public uploaded, have your ssh security group added. 
   
   Then create an instance using the image of <b> ubuntu 14.04-LTS-i386</b>, and assign your uploaded ssh public key pair to the instance and set 'inet' as the network. Flavor should be at least have 4, recommend 8GB RAM.
   
   After creating the instance, it will automatically give your a floating IP address, which is the IP address of your instance. 

3. ssh into the VM

    `$ ssh ubuntu@128.52.181.207` (the ip address here is random selected here and it should match your instance IP address )

4. Once you shh the VM, first install git using:

    `$ sudo apt-get install git`
  
   Then install the devstack:

    `git clone http://github.com/openstack-dev/devstack`
    
    `cd devstack`

   After install devstack, you need to create a new file called 'local.conf' and copy the following snippet to the file you just created:

    `$ sudo vim local.conf`

         #!/usr/bin/env bash
         [[local|localrc]]
         # Specify Neutron plugin to use
         Q_PLUGIN_CLASS=neutron.plugins.midonet.plugin.MidonetPluginV2

         # Load the devstack plugin for midonet
         enable_plugin networking-midonet http://github.com/openstack/networking-midonet.git

         # Load the LBaaS driver for midonet
         enable_plugin neutron-lbaas https://git.openstack.org/openstack/neutron-lbaas
         NEUTRON_LBAAS_SERVICE_PROVIDERV1="LOADBALANCER:Midonet:midonet.neutron.services.loadbalancer.
         driver.MidonetLoadbalancerDriver:default"

         enable_service horizon
   
   In the Q_PLUGIN_CLASS, we specify the version of kilo plugin that for installing midonet. 

   for more details, please go to [http://wiki.midonet.org/Devstack](http://wiki.midonet.org/Devstack) and [https://github.com/openstack/networking-midonet/tree/master/devstack](https://github.com/openstack/networking-midonet/tree/master/devstack)
5. Install the Devstack 
   `./stack.sh` 
   It will take a while to install. After installation, type the IP address in your browser and login through Horizon dashboard see if there is a tab called Network under the Project tab. 

Current Status:
   ** Successfully Install Devstack with Midonet Plugin on the instance hosted on CSAIL Opentack  **