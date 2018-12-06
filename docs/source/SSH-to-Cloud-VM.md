Before trying to access instances from the outside world, you need to make sure you have followed these steps:

* You followed the instruction in [[Create a Key Pair]] to set up a public ssh key.
* Your public ssh-key was selected (in the Access and Security tab) while [launching the instance](https://github.com/CCI-MOC/moc-public/wiki/Launch-a-VM).
* [[Assign a Floating IP]] to the instance in order to access it from outside world.
* Make sure you have added rules in the [[Security Groups]] to allow ssh to the instance.

Make note of the floating IP you have associated to your instance.

[[tutorial_screenshots/kilo/floatingip_is_associated.png]]

In our example, the IP is 129.10.3.66.

Default usernames for all the base images are:

* all Ubuntu images: ubuntu
* all CentOS images: centos
* all RHEL images: cloud-user

Our example VM was launched with the RHEL7.1 base image, the user we need is 'cloud-user'.  

Open a Terminal window and type:

      $ ssh cloud-user@129.10.3.66

Since you have never connected to this VM before, you will be asked if you are sure you want to connect.  Type `yes`. 

[[tutorial_screenshots/kilo/ssh_to_vm.png]]

Note that if you haven't added your key to ssh-agent, you may need to specify the private key file, like this:

      $ ssh -i ~/.ssh/cloud_key.pem cloud-user@129.10.3.66

If you have problems with the SSH step, this page lists solutions to some common problems: [[Troubleshooting SSH Login]]

***

#### Setting a password

When the VMs are launched, a strong, randomly-generated password is created for the default user, and then discarded.  Once you connect to your VM, you will want to set a password in case you ever need to log in via the console in the web dashboard - for example, if your network connections aren't working right.  

Since you are not using it to log in over SSH or to sudo, it doesn't really matter how hard it is to type, and we recommend using a randomly-generated password.  Create a random password like this:

     [cloud-user@test-vm ~]$ cat /dev/urandom | base64 | dd count=14 bs=1
     C8a6lipvDcLY6j14+0 records in
     14+0 records out
     14 bytes transferred in 0.030097 secs (465 bytes/sec)

The 'count' parameter controls the number of characters.  The first <count> characters of the output are your randomly generated output, followed immediately by "<count>+0", so in the above example the password is: `C8a6lipvDcLY6j` .

Set the password for cloud-user using the command `sudo passwd cloud-user`.  Store the password in a secure place.  Don't send it over email, post it on your wall on a sticky note, etc.

***

#### <a name="more-ssh-keys"></a>Adding other people's SSH keys to the instance.

You were able to log into using your own SSH key.  Right now Openstack only permits one key to be added at launch, so you need to add your teammates keys manually.

Get your teammates' public keys.  If they used ssh-keygen to create their key, this will be in a file called <key_name>.pub on their machine.  If they created a key via the dashboard, or imported the key created with ssh-keygen, their public key is viewable from the Key Pairs tab.  Click on the key pair name.  The public key starts with 'ssh-rsa' and looks something like this:

     ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDL6O5qNZHfgFwf4vnnib2XBub7ZU6khy6z6JQl3XRJg6I6gZ+Ss6tNjz0Xgax5My0bizORcka/TJ33S36XZfzUKGsZqyEl/ax1Xnl3MfE/rgq415wKljg4+QvDznF0OFqXjDIgL938N8G4mq/cKKtRSMdksAvNsAreO0W7GZi24G1giap4yuG4XghAXcYxDnOSzpyP2HgqgjsPdQue919IYvgH8shr+sPa48uC5sGU5PkTb0Pk/ef1Y5pLBQZYchyMakQvxjj7hHZaT/Lw0wIvGpPQay84plkjR2IDNb51tiEy5x163YDtrrP7RM2LJwXm+1vI8MzYmFRrXiqUyznd test_user@demo

Create a file called something like 'teammates.pub' and paste in your team's public keys, one per line.  Hang onto this file to save yourself from having to do all the copy/pasting every time you launch a new vm.

Copy the file to the vm:

     [you@your-laptop ~]$ scp teammates.pub cloud-user@129.10.3.66:~

If the copy works, you will see the output:

     testfile.txt                  100%    0     0.0KB/s   00:00 

Append the file's contents to authorized_keys:

     [cloud-user@test-vm ~]# cat testfile >> ~/.ssh/authorized_keys

Now your teammates should also be able to log in.

Make sure to use `>>` instead of `>` to avoid overwriting your own key.

***

#### <a name="more-users"></a>Adding users to the instance.

You may decide that each teammate should have their own user on the VM instead of everyone logging in to the default user. 

Once you log into the vm, you can create another user like this.  Note that the 'sudo_group' is different for different OS - in CentOS and Red Hat, the group is called 'wheel', while in Ubuntu, the group is called 'sudo'.

     $ sudo su
     # useradd -m <username>
     # passwd <username>
     # usermod -aG <sudo_group> <username>    <-- skip this step for users who should not have root access
     # su username
     $ cd ~
     $ mkdir .ssh
     $ chmod 700 .ssh
     $ cd .ssh
     $ vi authorized_keys   <------- paste the public key for that user here
     $ chmod 600 authorized_keys


***
#### Next: [[Volumes]]  
###### Previous:  [[Assign a Floating IP]]  
[[Openstack Tutorial Index]] 
