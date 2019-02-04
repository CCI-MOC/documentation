## OpenMPI on Slurm Cluster
There are two ways to install Open MPI on a Slurm cluster 
 1. Install and configure Open MPI libraries while installing Slurm on each node.  
 1. Install and configure Controller nodes and Compute nodes with the Open MPI libraries.  
  
The script follows the method 1. Whereas this steps can be done manually after Slurm is installed on the system.  
  
### On the Server/Controller and Compute node 
 1. **Download the openmpi tar file**. For the example purpose openmpi-1.10.2 is used throughout.  
```shell
        sudo wget https://www.open-mpi.org/software/ompi/v1.10/downloads/openmpi-1.10.2.tar.gz
```
 `. **Unextract the tar downloaded**.  
```shell
        tar -xvf openmpi-1.10.2.tar.gz
```
 This will create a directory openmpi-1.10.2 in the home directory.
 1. **Move into the openmpi directory**   
```shell
        cd openmpi-1.10.2
```
 1. **Run the configure file with the specified parameters**  
```shell
        ./configure --with-slurm --with-pmi --prefix="/home/$USER/.openmpi"  
```
 1. **To recompile the necessary packages run the command** 
```shell
        make
```
 1. **Run the make install command now**
```shell
        sudo make install  
```
 1. Now **add the path of the library to the current environment variable**  
```shell
        export PATH="$PATH:/home/$USER/.openmpi/bin"
        export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/home/$USER/.openmpi/lib/"  
```
 1. **Add this path to** `.bashrc`, `.cashr`, any other shell scripting present on the system..  
```shell
        echo export PATH="$PATH:/home/$USER/.openmpi/bin" >> /home/$USER/.bashrc
        echo export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/home/$USER/.openmpi/lib/">>/home/$USER/.bashrc  
```
Installing OpenMPI is done.

Now to use the OpenMPI over the cluster a NFS shared directory needs to be created on the Controller/Master Node 
and mounted on the same location on all the compute nodes.  
  
Install NFS on the nodes. This is to be followed in both the controller and compute node with few basic differences.  
  
### Controller/Server node configuration
 `. **Install NFS packages**  
```shell
        sudo yum -y install nfs-utils libnfsidmap libnfsidmap-devel  
```
 1. **Create a directory to be shared**  
```shell
        mkdir /home/centos/openmpi_shared_directory  
        sudo chmod 777 /home/centos/openmpi_shared_directory  
```
 1. Change the `/etc/exports` file, to **add the subnet/ip address and the directory that will be shared**
```shell
        sudo sh -c "echo -e '/home/centos/openmpi_shared_directory 192.168.100.0/24(rw,sync,no_root_squash,no_all_squash)' >> /etc/exports"
```
 Here "192.168.100.0/24" is the subnet address of all the compute nodes.  
 
 *NOTE:The NFS server needs to be restarted when you do this. As the server is yet not started we don't need to worry about that.* 
 1. **Enable and start the services**  
```shell
        sudo sh -c "echo -e '[Install]\nWantedBy=multi-user.target' >> /usr/lib/systemd/system/nfs-idmap.service"  
        sudo sh -c "echo -e '[Install]\nWantedBy=multi-user.target' >> /usr/lib/systemd/system/nfs-lock.service"  
```
 This makes sure that nfs-lock and nfs-idmap services have the Install section and can be started. nfs-lock and 
 nfs-idmap are linked to the rpc-statd.service and nfs-idmapd.service.  
  
 Enable the various services  
```shell
        sudo systemctl enable rpcbind  
        sudo systemctl enable nfs-server  
        sudo systemctl enable rpc-statd.service  
        sudo systemctl enable nfs-idmapd.service  
```
 Start the various services  
```shell
        sudo systemctl start rpcbind  
        sudo systemctl start nfs-server  
        sudo systemctl start nfs-lock  
        sudo systemctl start nfs-idmap  
```
 1. **Adding the services to the firewall**  
```shell
        sudo firewall-cmd --permanent --zone=public --add-service=nfs  
        sudo firewall-cmd --permanent --zone=public --add-service=mountd  
        sudo firewall-cmd --permanent --zone=public --add-service=rpc-bind  
```
 1. **Reloading the firewall**  
```shell
        sudo firewall-cmd --reload  
```

### Compute node configuration
 1. **Install NFS packages**  
```shell
        sudo yum -y install nfs-utils libnfsidmap libnfsidmap-devel  
```
 1. **Create the directory where the shared directory has to be mounted**  
```shell
        mkdir /home/centos/openmpi_shared_directory  
        sudo chmod 777 /home/centos/openmpi_shared_directory  
```
 1. **Enabling and starting the services**
```shell
        sudo sh -c "echo -e '[Install]\nWantedBy=multi-user.target' >> /usr/lib/systemd/system/nfs-idmap.service"  
        sudo sh -c "echo -e '[Install]\nWantedBy=multi-user.target' >> /usr/lib/systemd/system/nfs-lock.service"  
```
 This makes sure that nfs-lock and nfs-idmap services have the Install section and can be started. nfs-lock and 
 nfs-idmap are linked to the rpc-statd.service and nfs-idmapd.service.  
  
  Enable the various services  
```shell
        sudo systemctl enable rpcbind  
        sudo systemctl enable nfs-server  
        sudo systemctl enable rpc-statd.service  
        sudo systemctl enable nfs-idmapd.service  
```
  Start the various services  
```shell
        sudo systemctl start rpcbind  
        sudo systemctl start nfs-server  
        sudo systemctl start nfs-lock  
        sudo systemctl start nfs-idmap  
```
 1. **Mount the directory**  
```shell
        sudo mount -t nfs 192.168.100.100:/home/centos/openmpi_shared_directory/ /home/centos/openmpi_shared_directory/  
```
 Here 192.168.100.100 is the ip address of the Controller/Server node.  

 If this command hangs for a while and/or gives connection timed out error. That means the firewall wasn't
 set up properly.  
  
 1. **Checking whether the directory was mounted**  

     To check whether the directory was mounted properly you can run the following command
```shell 
        df -h  
```
 This command should give a output like this  
```shell
        Filesystem                                             Size  Used Avail Use% Mounted on  
        /dev/vda1                                               20G  2.4G   18G  12% /  
        devtmpfs                                               984M     0  984M   0% /dev  
        tmpfs                                                 1001M     0 1001M   0% /dev/shm  
        tmpfs                                                 1001M   89M  913M   9% /run  
        tmpfs                                                 1001M     0 1001M   0% /sys/fs/cgroup  
        192.168.100.100:/home/centos/openmpi_shared_directory   20G  2.4G   18G  12% /home/centos/openmpi_shared_directory  
```
 The last line in the section above shows the mounted directory from the Controller/Server node.
