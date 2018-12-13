Use the Packstack "allinone" option to deploy an Openstack-in-a-box.  This tends to work better than trying to get DevStack to work on CentOS.

1. First, create a CentOS 7 VM.  Make sure to use a flavor with enough memory.  If you will only be running PackStack, `m1.large` will work.  If you want to deploy other services in the same VM, use `m1.xlarge`.

**IMPORTANT: The steps below should be done INSIDE THE VM, *NOT* directly on your computer**   
Installing PackStack on your computer will probably make it stop working.

2. In the VM, add this line to the file `/etc/resolv.conf`:    
     `options single-request`

   And add this line to `/etc/sysconfig/network`:    
      `RES_OPTIONS=single-request`

3. Follow these instructions to install and deploy PackStack     
   *Note: the MOC Kaizen Openstack cluster is currently running OpenStack Mitaka, but if you want a different version, you can replace 'mitaka' in the first command below with that release*

    $ sudo yum install -y centos-release-openstack-mitaka
    $ sudo yum update -y
    $ sudo yum install -y openstack-packstack
    $ sudo packstack --allinone

    The last command takes a long time so just let it run.  When it is finished, you should see a message like this:
```
**** Installation completed successfully ******

Additional information:
 * A new answerfile was created in: /root/packstack-answers-20170601-190213.txt
 * Time synchronization installation was skipped. Please note that unsynchronized time on server instances might be problem for some OpenStack components.
 * File /root/keystonerc_admin has been created on OpenStack client host 10.11.12.13. To use the command line tools you need to source the file.
 * To access the OpenStack Dashboard browse to http://10.11.12.13/dashboard .
Please, find your login credentials stored in the keystonerc_admin in your home directory.
 * The installation log file is available at: /var/tmp/packstack/20170601-190212-ESb_Vc/openstack-setup.log
 * The generated manifests are available at: /var/tmp/packstack/20170601-190212-ESb_Vc/manifests
```

4. Get the admin credentials for your OpenStack cluster from the file `/root/keystonerc_admin`.  Test that your dashboard is working by navigating to `http://<floating-IP-of-your-instance>/dashboard` and logging in.  

If your instance does not have a floating IP, but another instance on the same private network does, you can use this command to forward a port on your local machine:

`ssh -L 8080:10.11.12.13  <user>@<host-with-floating-ip>`

where `<user>` and `<host-with-floating-ip>` point to the host which has a public IP.  After the ssh connection is live, you can point your browser to localhost:8080/dashboard and the login page should appear.