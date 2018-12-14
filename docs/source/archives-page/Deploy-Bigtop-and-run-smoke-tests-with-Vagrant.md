Apache Bigtop is a project for the development of packaging and test of the Hadoop ecosystem. 

## Deployment 
  Deploy a Bigtop virtual Hadoop cluster with vagrant and puppet recipe
  Github repo => https://github.com/apache/bigtop/tree/master/bigtop-deploy/vm/vagrant-puppet-vm

####1. Clone the Bigtop repository
     $ git clone https://github.com/apache/bigtop.git
     $ cd bigtop/bigtop-deploy/vm/vagrant-puppet-vm 

####2. Install plugins 
     $ vagrant plugin install vagrant-hostmanager  
     $ vagrant plugin install vagrant-cachier

####3. Configure Vagrantfile and vagrantfile.yaml

  **this modification is no longer needed** because of the [recent update](https://github.com/apache/bigtop/commit/46d0a72399c12cca98e9bac7e354e5cb18ec78be) in apache/bigtop git repo 

  The vagrantfile.yaml works as a configuration file of Vagrantfile. 
  They handle the initialization of vagrant box automatically. The default box in vagrantfile.yaml is centos6.6
  For basic deployment, there's no need to change anything in vagrantfile.yaml 
  However, we have to make some small modification in Vagrantfile, add two commands. 
  
    68 $script = <<SCRIPT
    69 service iptables stop
    70 chkconfig iptables off
    +  71 puppet module install puppetlabs-stdlib
    72 cat /dev/null > /etc/hosts
    73 echo "Bigtop yum repo = #{repo}"
    74 # Prepare puppet configuration file
    75 mkdir -p /etc/puppet/hieradata
    + 76 mkdir -p /data/1 /data/2

  The vagrantfile.yaml also has other options that users can specify 
  such as: change number of nodes to provision in bigtop cluster
     
     num_instances: 5
     
  see their github repo for more infomation

####4. Spin up Bigtop Cluster
     
     vagrant up 

## Smoke tests

  set `run_smoke_tests: true` in vagrantconfig.yaml

  * this will execute utils/smoke-tests.sh while `vagrant up`
    * this script will install pig, hive, flume, sqoop, mahout to the cluster. (they don't seem to have smoke tests on spark which is weird) 
    * and execute default smoke tests on mapreduce and pig. 
    * to run more smoke tests look at **section 3. GO** 
  
####1. Spin up Cluster and ssh into it
   
    vagrant up
    vagrant ssh

####2. Export some environment variables 

    export HADOOP_CONF_DIR=/etc/hadoop/conf/
    export BIGTOP_HOME=/bigtop-home/
    export HADOOP_MAPRED_HOME=/usr/lib/hadoop-mapreduce/
    export HIVE_HOME=/usr/lib/hive/
    export PIG_HOME=/usr/lib/pig/
    export FLUME_HOME=/usr/lib/flume/
    export HIVE_CONF_DIR=/etc/hive/conf/
    export JAVA_HOME=/usr/lib/jvm/java-openjdk/
    export MAHOUT_HOME=/usr/lib/mahout
    export SQOOP_HOME=/usr/lib/sqoop
    export ITEST="0.7.0"

####3. GO
  move to smoke tests directory and switch to root user  

    cd /bigtop-home/bigtop-tests/smoke-tests

  for example: run smoke tests on flume and hive

    ./gradlew compileGroovy test -Dsmoke.tests=flume,hive --info

  `--info` option helps debugging, can change to `--debug` if needed more detail

