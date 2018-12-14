##Vagrant Setup
####1.Install Vagrant and Virtual Box

    currently using ubuntu/trusty64
    https://atlas.hashicorp.com/ubuntu/boxes/trusty64

####2.Create directory 

    $ mkdir devstack_vagrant
    $ cd devstack_vagrant

####3.Start Vagrant

    $ vagrant box add precise32 http://files.vagrantup.com/precise32.box
    $ vagrant init precise32
    (or 64bit image, merely change 32 to 64)

####4.Open Vagrantfile and increase the amount of RAM allocated to VM

    $ vim Vagrantfile
    
  edit the following line to match the code as below:

      config.vm.provider "virtualbox" do |vb|
      #   # Display the VirtualBox GUI when booting the machine
         vb.gui = true
      #
      #   # Customize the amount of memory on the VM:
      vb.memory = "4096"
      end
  
  this allocates 4G RAM to the VM, and allow VirtualBox gui
  save and close file

  edit the following line to match the code as below:

      config.vm.provision "shell", inline: <<-shell
      # This following shell commands update the vm and install necessary software  
        sudo apt-get update 
        sudo apt-get -y install git vim-gtk libxml2-dev libxslt1-dev libpq-dev python-pip libsqlite3-dev 
        sudo apt-get -y build-dep python-mysqldb 
        sudo pip install git-review tox
        git clone git://git.openstack.org/openstack-dev/devstack && cd devstack
 
      # This following commands creates stack user, and start devstack 
        cd ~/devstack
        sudo ./tools/create-stack-user.sh
        sudo cp samples/local.conf localrc 
        sudo chown -R stack ~/devstack
        # You can choose to run stack.sh here without specific configuration
        # or ssh in vagrant, and change the localrc file in ~/devstack directory
        # ./stack.sh        
      shell

 -possible error: If see python2.7/pip error relate to setuptools.egg-info file, run:

    $ sudo rm /usr/lib/python2.7/dist-packages/setuptools.egg-info
    $ sudo apt-get install --reinstall python-setuptools

####5.Vagrant up

    $ vagrant up 
    $ vagrant ssh

   At this point, you should be in vagrant

### Configuration in Devstack
Edit the devstack/localrc file to match the following (minimal configuration): 

    [[local|localrc]]
    ADMIN_PASSWORD=secrete
    DATABASE_PASSWORD=$ADMIN_PASSWORD
    RABBIT_PASSWORD=$ADMIN_PASSWORD
    SERVICE_PASSWORD=$ADMIN_PASSWORD
    SERVICE_TOKEN=a682f596-76f3-11e3-b3b2-e716f9080d50
    #FIXED_RANGE=172.31.1.0/24
    #FLOATING_RANGE=192.168.20.0/25
    #HOST_IP=10.3.4.5
save and close file

For more information: [[http://docs.openstack.org/developer/devstack/configuration.html]]

#### Run Devstack

    $ ./stack.sh

#### Restarting ./stack.sh error
ERROR 1045 (28000): Access denied for user ‘root’@’localhost’ (using password: YES)
mysql connectivity error. Because keys don't match.
uninstall mysql completely, devstack will download all packages when ./stack.sh

    $ sudo service mysql stop  #or mysqld
    $ sudo apt-get remove --purge mysql-server mysql-client mysql-common
    $ sudo apt-get autoremove
    $ sudo apt-get autoclean
    $ sudo deluser mysql
    $ sudo rm -rf /var/lib/mysql

#### ./rejoint-stack.sh Error
Error Message: "Cannot open your terminal '/dev/pts/0' - please check."

More info: (http://makandracards.com/makandra/2533-solve-screen-error-cannot-open-your-terminal-dev-pts-0-please-check)
     
     run 
     $ script /dev/null
     $ screen 

#### log in to Horizon

socks proxy 
ssh vagrant@127.0.0.1 -p 2222 -D 8888
(password: vagrant)

set browser setting -> advance -> network -> setting -> port# 8888
