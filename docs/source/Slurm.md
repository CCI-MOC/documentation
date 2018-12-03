# SLURM
**SLURM** is an open-source workload manager designed for Linux clusters of all sizes. 

It provides three key functions:
* First it allocates exclusive and/or non-exclusive access to resources (computer nodes) to users for some duration of time so they can perform work.
* Second, it provides a framework for starting, executing, and monitoring work (typically a parallel job) on a set of allocated nodes.
* Finally, it arbitrates contention for resources by managing a queue of pending work.

## Contents:  
1. [SLURM Installation](#slurm-installation)  
2. [MUNGE](#munge)  
3. [Build SLURM RPMs](#build-slurm-rpms)  
4. [Configure SLURM](#configure-slurm)  
5. [Configure firewall for SLURM daemons](#configure-firewall-for-slurm-daemons)  
6. [Checking the SLURM daemons](#checking-the-slurm-daemons)  
7. [Testing basic functionality](#testing-basic-functionality)  
8. [References](#references)

******  
### SLURM Installation  
SLURM and MUNGE require consistent UID and GID across all servers and nodes in the cluster. Create the users/groups for slurm and munge, for example:  

    export MUNGEUSER=991  
    groupadd -g $MUNGEUSER munge  
    useradd  -m -c "MUNGE Uid 'N' Gid Emporium" -d /var/lib/munge -u $MUNGEUSER -g munge  -s /sbin/nologin munge  
    export SLURMUSER=992  
    groupadd -g $SLURMUSER slurm  
    useradd  -m -c "SLURM workload manager" -d /var/lib/slurm -u $SLURMUSER -g slurm  -s /bin/bash slurm  

These same user accounts should be created identically on all nodes. This must be done prior to installing RPMs(which would create random UID/GID pairs if these users don't exist).

### MUNGE   
**MUNGE** is a authentication plugin that identifies the user originating a message. There are three functional authentication plugins available namely authd, Munge and none. Out of which Munge is used over here.  

The MUNGE RPM package for RHEL7 is in the EPEL repository, where you install the newest version of epel-release RPM for EL7, for example:  

    CentOS: yum install epel-release  

Now install the MUNGE RPM packages from the EPEL repository:  

    yum install munge munge-libs munge-devel  

### MUNGE configuration and testing  

**Create a secret key**  

    /usr/sbin/create-munge-key 

This uses /dev/urandom (Non-blocking pseudorandom number generator) to generate the key.  
  
**Change the ownership of the munge.key**  

    chown munge: /etc/munge/munge.key
    chmod 400 /etc/munge/munge.key  

After creating the `munge.key` securely propagate `/etc/munge/munge.key` (e.g., via SSH) to all other hosts within the same security realm. Make sure to set the correct ownership etc. On all the nodes:  

    chown -R munge: /etc/munge/ /var/log/munge/
    chmod 0700 /etc/munge/ /var/log/munge/  
  
**Enable and start the MUNGE service**

    systemctl enable munge
    systemctl start munge  
  
**Run some tests** as described in the [Munge installation](https://github.com/dun/munge/wiki/Installation-Guide) guide:  

    munge -n                    # Generate a credential on stdout
    munge -n | unmunge          # Displays information about the MUNGE key  
    munge -n | ssh somehost unmunge  
   remunge  

Follow the link to [Munge installation](https://github.com/dun/munge/wiki/Installation-Guide) guide for a more information.  

### Build SLURM RPMs  
**Install SLURM prerequisites as well as several optional packages**  

    yum install openssl openssl-devel pam-devel numactl numactl-devel hwloc hwloc-devel lua lua-devel readline-devel rrdtool-devel ncurses-devel man2html libibmad libibumad perl-CPAN perl-Switch  

**Install the MariaDB(a replacement for MySQL) packages before you build SLURM RPMs(otherwise some libraries will be missing)**  

    yum install mariadb-server mariadb-devel  
  
**Now to download the SLURM bzip2 file run the command**  

    sudo wget www.schedmd.com/download/archive/slurm-x.y.z.tar.bz2  
  
**Now to build SLURM RPMs, run the command**

    rpmbuild -ta slurm-x.y.z.tar.bz2  

The slurm-x.y.z in the above two commands should be replaced with the version of SLURM that has to be used(e.g., slurm-15.08.7).  
  
The rpmbuild command will create directory named `rpmbuild` in the home directory. All the .rpm files will be present in the `~/rpmbuild/RPMS/x86_64/_`  
  
The RPMs needed on the head node(controller node), compute nodes and slurmdbd node can vary by configuration, but here is a suggested starting point:  
* **Head Node**(where the slurmctld daemon runs), **Compute** and **Login Nodes** :  

        slurm-plugins  
        slurm  
        slurm-devel  
        slurm-munge  
        slurm-perlapi  
        slurm-sjobexit  
        slurm-sjstat  
        slurm-torque  
  
* **Slurmdbd Node** :  
    
        slurm-plugins  
        slurm  
        slurm-devel  
        slurm-munge  
        slurm-slurmdbd  
        slurm-sql  
  
The RPMs perform the configurations, but permissions on `/etc/munge/` and `/var/log/munge/` should be 0700.  

******
### Configure SLURM  
To configure SLURM and let all the nodes in the cluster know about each other a configuration file needs to be placed on all the nodes in cluster. This file is called `slurm.conf`.  

There are two ways to create a `slurm.conf` file after the SLURM packages are installed.  
1. After the SLURM RPMs are installed a directory `/etc/slurm/`  is created. Under that directory you will find `slurm.conf.example`. You can copy this file to `slurm.conf`  by running the command:  
```
cp /etc/slurm/slurm.conf.example /path_to_directory/slurm.conf  
```  
Where `path_to_directory` can be any directory of your choosing.  
2. After the SLURM RPMs are installed, under the directory `/usr/share/doc/slurm-x.y.z/html`  you will find a file named `configurator.easy.html`. Open this file in any web browser of your choosing and enter the configuration details as needed and click the submit button.  Now copy the information shown on the resulting page into `slurm.conf` (this file needs to created in the `/etc/slurm/` directory) and save the file.  

In `/etc/slurm/slurm.conf` the important spool directories and slurm user are defined:  

    SlurmUser=slurm  
    SlurmdSpoolDir=/var/spool/slurmd  
    StateSaveLocation=/var/spool/slurmctld

*NOTE: This spool directories are created manually by the commands in the following sections.* 
  
### Compute node configuration  
Create `slurmd` spool directory and make the correct ownership:  

    mkdir /var/spool/slurmd  
    chown slurm: /var/spool/slurmd  
    chmod 755 /var/spool/slurmd  

Create log files:  

    touch /var/log/slurmd.log  
    chown slurm: /var/log/slurmd.log  

Executing the command:  

    slurmd -C  

On each compute node will print its physical configuration (sockets, cores, real memory size, etc.), which must be added to the global `/etc/slurm/slurm.conf` file. For example a node may be defined as:  

    NodeName=test001 Boards=1 SocketsPerBoard=2 CoresPerSocket=2 ThreadsPerCore=1 RealMemory=8010 TmpDisk=32752 Feature=xeon  

Start and enable the slurmd daemon:  

    systemctl enable slurmd.service
    systemctl start slurmd.service
    systemctl status slurmd.service  

Some `defaults` may be configured for similar compute nodes, for example:  

    NodeName=DEFAULT Boards=1 SocketsPerBoard=2 CoresPerSocket=2 ThreadsPerCore=1 RealMemory=8000 TmpDisk=32752  
    NodeName=q001  
    NodeName=q002  
    ...  

### Master server configuration  
Create the spool directories and make them owned by the slurm user:  

    mkdir /var/spool/slurmctld  
    chown slurm: /var/spool/slurmctld  
    chmod 755 /var/spool/slurmctld  
  
Create log files:  

    touch /var/log/slurmctld.log
    chown slurm: /var/log/slurmctld.log

Create the (Linux default) accounting file:  

    touch /var/log/slurm_jobacct.log /var/log/slurm_jobcomp.log  
    chown slurm: /var/log/slurm_jobacct.log /var/log/slurm_jobcomp.log  
  
Start and enable the slurmctld daemon:  

    systemctl enable slurmctld.service  
    systemctl start slurmctld.service  
    systemctl status slurmctld.service  
  
### Configure firewall for SLURM daemons  
To allow the SLURM compute nodes must be allowed to connect to the central controller's `slurmctld` daemon. In the configuration file these ports are configured:  

    SlurmctldPort=6817  
    SlurmdPort=6818  
    SchedulerPort=7321  
  
**CentOS7/RHEL7**

The CentOS7/RHEL7 default firewall service is [firewalld](https://fedoraproject.org/wiki/FirewallD) and **not** the well-known iptables service. 

The dynamic firewall daemon [firewalld](https://fedoraproject.org/wiki/FirewallD) provides a dynamically managed firewall with support for network “zones” to assign a level of trust to a network and its associated connections and interfaces.

See [Introduction to firewalld](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Security_Guide/sec-Using_Firewalls.html) to learn more about firewalld.  
  
* **Install firewalld** with:
```
yum install firewalld firewall-config  
```
* To **configure Controller node**, whitelist the compute nodes' private subnets (here: 192.168.x.x) on the controller node:  
```
systemctl start firewalld
firewall-cmd --permanent --direct --add-rule ipv4 filter INPUT_direct 0 -s 192.168.x.x/24 -j ACCEPT  
```
The configuration is stored in the file `/etc/firewalld/direct.xml`.  
  
* To **configure compute nodes**, Run the following commands on the compute nodes:  

    systemctl start firewalld  
    sudo firewall-cmd --zone=public --add-port=6818/tcp  #Adding the Slurmd Port 6818 (TCP) in the public zone.
    sudo systemctl stop firewalld  
    sudo systemctl disable firewalld

******
### Checking the SLURM daemons  
Check the configured daemons using the scontrol command:  

    scontrol show daemons  
  
To verify the basic cluster partition setup:  

    scontrol show partition  

To display the SLURM configuration:  

    scontrol show config  

To display the compute nodes:  

    scontrol show nodes  
  
### Testing basic functionality  
From the master node try to submit an interactive job:  

    srun -N1 /bin/hostname  

If srun hangs, check the firewall settings described above.  
  
To display the job queue:  

    scontrol show jobs  
  
To submit a batch job script:  

    sbatch -N1 <script-file>  

******
### References  
1. [SLURM Quick Start](http://www.schedmd.com/slurmdocs/quickstart_admin.html)  
2. [SLURM Quick Start Tutorial](http://www.ceci-hpc.be/slurm_tutorial.html)  
3. [SLURM Design](http://slurm.schedmd.com/slurm_design.pdf)  
4. [SLURM User Guide](https://www.unila.edu.br/sites/default/files/files/user_guide_slurm.pdf)
5. [SLURM Basic configuration and Usage](http://slurm.schedmd.com/slurm_ug_2011/Basic_Configuration_Usage.pdf)

