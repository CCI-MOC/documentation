## Staging area foreman
IP: 10.14.37.1 (reachable from 129.10.3.16 and 129.10.3.39)
```
[root@dev-foreman1 environments]# pwd
/etc/puppet/environments
[root@dev-foreman1 environments]# ls
production  staging_gen  staging_laura  staging_rado  staging_rahul
[root@dev-foreman1 environments]# 
```

## Various staging environments
These VMs are running on 10.14.37.25 node. You only need access to your hosts as mentioned below:-
```
1. staging_tammy
   Controller: 10.14.37.201
   Compute:    10.14.37.202

2. staging_laura
   Controller: 10.14.37.203
   Compute:    10.14.37.204

3. staging_rahul
   Controller: 10.14.37.205
   Compute:    10.14.37.206

4. staging_rado
   Controller: 10.14.37.207
   Compute:    10.14.37.208

5. staging_ruoyu
   Controller: 10.14.37.215
   Compute:    10.14.37.216
```
If you want to clone your code to the staging environment, you can delete <your-env>/modules directory and clone your github-repository here. Make sure you copy the certs after cloning the repository. Steps to get the certs is mentioned below in "creating a new environment" section.

## Creating a new environment
* Create/allocate two nodes and assign them to staging foreman (10.14.37.1)
* Create a new environment for your nodes. Steps for an environment named test:
```
# cd /etc/puppet/environments
# mkdir test
# cd test
# mkdir manifests
# git clone https://github.com/CCI-MOC/kilo-puppet modules
```
* Import this environment on the foreman.
* Create two hostgroups: controller_test and compute_test. For controller_test, select the class as "quickstack::neutron::controller" and for compute_test, select "quickstack::neutron::compute"
* Assign each hostgroup to individual nodes.
* Create a hiera file in /var/lib/hiera/test.yaml. You can copy the existing ones.
* Add the certs. Here is an example of copying the reference certs from production environment to test environment.
```
cp -r /etc/puppet/environments/production/modules/moc_openstack/files/certs /etc/puppet/environments/test/modules/moc_openstack/files/
cp -r /etc/puppet/environments/production/modules/moc_openstack/files/nova /etc/puppet/environments/test/modules/moc_openstack/files/
```
* Puppetize the nodes.

#### Setting up tempest
https://github.com/CCI-MOC/moc/wiki/Setting-up-tempest

#### Midonet
https://github.com/CCI-MOC/moc/wiki/Midonet