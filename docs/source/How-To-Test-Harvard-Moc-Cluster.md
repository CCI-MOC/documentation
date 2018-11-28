### Motivation

Test the Harvard MOC cluster against the test shell script and document the process.

### Pre-Requisite

Host Machine: Mac OSX
VM -> Linux Trusty
VM installed using Vagrant + VirtualBox

Ensure you have access to moc-ssh-gateway on your host machine.

#### Install Ubuntu using Vagrant
Create a directory in which you would create a vagrant machine.  

    $ mkdir vagrant

Another directory to label for what purpose you are creating the machine.  

    $ mkdir test-moc
    $ cd vagrant/test-moc/

You can view a list of vagrant machines at this link or you can create vagrant box:    
[vagrant machines](https://atlas.hashicorp.com/boxes/search)  
Run the following cmds to setup vagrant machine in lets say terminal 1.  

    $ vagrant init ubuntu/trusty64
    $ vagrant up

In a new terminal - term 2:  

    $ vagrant ssh
    $ ping 8.8.8.8  // checking of you can access outside network(the network config is NAT enabled)  
    $ exit

Back in terminal -1, lets halt the vm as to configure the vagrant file:  

   $ vagrant halt

Edit the Vagrant file to attach an IP address to your newly created vm:

    Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
        config.vm.network "private_network", ip: "192.168.33.10"   
    end

#### Enable ssh forwarding for Vagrant

Edit the ssh config file on your host machine:  

    $ emacs ~/.ssh/config

Add the following Host info to your config file. This will enable ssh agent  
forwarding with vagrant.

    Host vm-ip
        ForwardAgent yes

where vm-ip in my case is "192.168.33.10".     

Run the following ssh command to add the new identity:

    $ ssh-add 
     
Edit the Vagrant file and uncomment the following line:

    config.ssh.forward_agent = true

Launch and login to vagrant machine on another terminal.  
At this point running the following command on both the host machine and the vagrant machine. 

    $ ssh-add -L
    
The above command should list the key identities(should be same on both the machines).   

### How To tunnel openstack calls through moc ssh gateway

#### Install proxychains

Run the following commands on your vm ,in this example the vbox vm
   
    $ apt-get install proxychains
    $ vim /etc/proxychains.conf

    - // Comment out existing socks line at the end of file
    socks5 127.0.0.1 8000  //add this line
    
    The above config will tunnel commands run with "proxychains" through port 8000 on the local host.

#### Install OpenStack Python client libraries.

    In your vm install openstack python client: 
    $ sudo apt-get install python-openstackclient  

    // Some of the core packages installed will be listed at the end of installation:  

    Setting up python-cinderclient (1:1.0.8-0ubuntu2) ...
    Setting up python-cliff (1.4.5-1ubuntu2) ...
    Setting up python-netaddr (0.7.10-1ubuntu1.1) ...
    Setting up python-iso8601 (0.1.10-0ubuntu1) ...
    Setting up python-novaclient (1:2.17.0-0ubuntu1) ...
    Setting up python-keystoneclient (1:0.7.1-ubuntu1) ...
    Setting up python-glanceclient (1:0.12.0-0ubuntu1) ...
    Setting up python-openstackclient (0.3.0-1ubuntu1) ...
    Setting up python-cliff-doc (1.4.5-1ubuntu2) ... 
   

  Lets refer this page [OpenStack CLI clients](http://docs.openstack.org/user-guide/content/install_clients.html)
  and install the other openstack client libraries required for testing.  

    $ apt-get install python-ceilometerclient
    $ apt-get install python-heatclient
    $ apt-get install python-neutronclient
    $ apt-get install python-swiftclient      

#### Set up the test script for testing
Copy the test files openstack_test.sh and george.yaml from [harvard cluster wiki](Harvard-Cluster.html) in a new folder inside your vagrant box/vm.

Change permission level for the test script

    $ chmod +x openstack_test.sh

### Steps to run openstack client api's using proxychain against the Cluster

One another terminal:  

    $ ssh -D 8000 -N your_username@140.247.152.200 

Note: the commands in test script to test volume need to be uncommented.  
Lets start testing !    

    $ proxychains ./openstack.sh
    The output should looks like the follows:
     |S-chain|-<>-127.0.0.1:8000-<><>-140.247.152.207:35357-<><>-OK
     |S-chain|-<>-127.0.0.1:8000-<><>-140.247.152.207:9696-<><>-OK....
     // The first time you would get the following message and have a bash shell to interact with.  
     Test server at 140.247.152.21 now. There should be a volume attached.
     $ 
     // Wait for a minute and then try to ssh into the vm which is cirros in this case.
     $ ssh cirros@140.247.152.21 -i /tmp/testkey.pem

     // You will get the following output:
     The authenticity of host '140.247.152.21 (140.247.152.21)' can't be established.
     RSA key fingerprint is d7:51:df:e1:f8:4e:77:b1:29:f0:38:ae:da:da:40:07.
     Are you sure you want to continue connecting (yes/no)? "yes"
     $ "exit"  //type exit to go back the bash shell of the running script
     vagrant@vagrant-ubuntu-trusty-64:~/lets-test$ "exit"   //type exit again to continue the script
     // The script might halt again after detaching the volume from the previous instance and prompt the 
     // following:
     Test server at 140.247.152.21 now.
     vagrant@vagrant-ubuntu-trusty-64:~/lets-test$
     Note: You can execute the openstack CLI like following:
     vagrant@vagrant-ubuntu-trusty-64:~/lets-test$ proxychains nova list
     // Look under References at the end of page to refer to the cheat sheet for cmds. 

     The Heat manifest sets up two servers.  
     Test your heat stack now

     vagrant@vagrant-ubuntu-trusty-64:~/lets-test$ proxychains nova list
     ProxyChains-3.1 (http://proxychains.sf.net)
     |S-chain|-<>-127.0.0.1:8000-<><>-140.247.152.207:35357-<><>-OK
     |S-chain|-<>-127.0.0.1:8000-<><>-140.247.152.207:8774-<><>-OK

  continuation ....

`| ID                                 | Name    | Status | Task State | Power State | Networks|`

`|376a149b-363c-46c7-a3d5-37065d769f38| Server1 | ACTIVE | - | Running  | borr=192.168.4.4, 140.247.152.186 |`

`|747d96b4-9169-423c-a084-77a4b22b94ac| Server2 | ACTIVE | - | Running  | borr=192.168.4.2, 140.247.152.188 |` 
 
    // Wait for few minutes before you test the new instances Server1 and Server2 and then type "exit" to continue 
    // the testing.  

    vagrant@vagrant-ubuntu-trusty-64:~/lets-test$ "exit"

    // The script would clean up the resources created.
    Done !!

Testing is Complete !

#### References

[OpenStack CLI cheat sheet](http://docs.openstack.org/user-guide/content/app_cheat_sheet.html)  
     
