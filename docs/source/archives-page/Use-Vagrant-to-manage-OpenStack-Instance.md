###1. Get the github repo 
     
      $ git clone https://github.com/ggiamarchi/vagrant-openstack-provider

###2. Download a vagrant box that's compatible with OpenStack

      for example ubuntu precise32
      $ vagrant box add hashicorp/precise32
      $ vagrant init hashicorp/precise32 

###3. Download Openstack RC file

      go to Openstack Dashboard => Project => Compute => Access and Security
      Upper right conner, click Download OpenStack RC File
      Then run 
      $ source [openstack rc file path]

###4. Modify Vagrantfile to match the following

      This Vagrant file will launch an instance in your OpenStack project

      require 'vagrant-openstack-provider'                        
      Vagrant.configure(2) do |config|
        config.vm.box = "hashicorp/precise32"
        config.ssh.username = 'ubuntu'
        config.vm.provider :openstack do |os|
          os.openstack_auth_url = 'http://140.247.152.207:5000/v2.0/tokens'  # our production environment 
          os.username           = ENV['OS_USERNAME']
          os.password           = ENV['OS_PASSWORD']
          os.tenant_name        = ENV['OS_TENANT_NAME']
          os.flavor             = 'm1.small'                                 # your choice of flavor
          os.image              = '5e39e68a-dd23-4f40-a8c1-c156f1214cc3'     # this is a ubuntu image
          os.floating_ip_pool   = 'subnet1'                                  # change to your own
                                                                             # floating ip pool 
          os.endpoint_type      = 'publicURL'
        end
      end


###5. GO
      
      - install openstack provider plugin
        $ vagrant plugin install vagrant-openstack-provider
      - vagrant up
        $ vagrant up --provider=openstack
        ***** IF YOU ARE USING PROXYCHAINS, RUN *****
        $ proxychains vagrant up --provider=openstack
      - vagrant ssh
        $ vagrant ssh 
        You should be in vagrant now
        use the ssh username you specified in Vagrantfile to ssh into the vm you just create 
      - Debug options
        $ export VAGRANT_OPENSTACK_LOG=debug 
        This sets vagrant to debug mode, and will output more details