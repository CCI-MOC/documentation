Reprovision Staging Environment


1. Download and Launch X2Go client
2. Connect to Node 39 
3. Now, connect to the foreman in a browser through node 39 from X2Go client (foreman ip: 10.14.37.1)
4. Select hosts -> all hosts
5. Find your staging environment that you want to reprovision, and click on it
6. Click build in the top right. If it shows an error, follow next steps:
      1. Click “edit” next to build
      2. Go to interfaces. Delete all interfaces (except for one, which you will not be able to delete anyway)
      3. Then, go to Operating System. Change the operating system to Rhel_Server_7.1_StagingVM 7.1
      4. Change media to custom foreman mirror
      5. Change partition table to VM staging
      6. Click Submit
      7. Now, click build again, 
7. Click Build in the confirmation window. If you see “Warning: This will delete this host and all of its data!”, that is ok.
8. Through X2GoClient, connect from node 39 to the node where your staging environments are on top of (for me, 10.14.37.50 is hosting my nodes, so I ssh to it in the X2go terminal like this: ssh -X -Y root@10.14.37.50)
9. Launch virt-manager on this node. In the list of machines, you should see your staging environments
10. Select your staging environment in the virt-manager and click open. Then, in the new window, select the arrow at the top that gives you the options: “Reboot, Shut Down, Force Reset, Force Off”. Click Force reset. Then, quickly, while your node is resetting, hit escape. This will open the boot menu. If you do not hit escape quickly enough and the boot menu doesn’t open, force reset again and repeat.
11. Once you are in the boot menu, Select iPXE (PCI 00:005.0). For me this was the second boot option, so I pressed 2 
12. Now, your staging environment should be building.
13. Once it has finished building, go to the node where you ssh into your staging environment (for me, 39), and go to your home directory. Go to .ssh/known_hosts, and delete the ip of your staging environment. 
14. Now, ssh as root to your staging environment (ssh root@your_staging_ip). Input the password as “Password1” without the quotes (make sure P is capital).
15. Now, you are in your staging environment. Execute service puppet stop. 
16. Now, go to the foreman (10.14.37.1). Get sudo access (sudo su) and run puppet cert sign –all
17. Now, if you are reprovisioning a controller node:
      1. Go to /var/lib/hiera, open your staging environment.yaml file (for me staging_dimitri.yaml) and find the line that says hosts::host_entries. Mine looks like:
hosts::host_entries:
 'compute-215.staging.moc.edu':
    ip: '10.14.37.215'
    host_aliases:
     - 'keystone.staging.moc.edu'
     - 'glance.staging.moc.edu'
     - 'cinder.staging.moc.edu'
     - 'neutron.staging.moc.edu'
     - 'nova.staging.moc.edu'
      2. Copy the ip, host name, and host_aliases into your controller node’s /etc/hosts file. End result in the hosts file should look like:
      10.14.37.215 compute-215.staging.moc.eu nova.staging.moc.edu neutron.staging.moc.edu glance.staging.moc.edu keystone.staging.moc.edu cinder.staging.moc.edu
      3. (This should all be one line)
18. And you’re done! Run puppet twice – it will probably fail on the first run, but should succeed after that. The runs will take longer than normal.
19. Finally, after you run puppet, check the /home directory and make sure you are still a user there. It may have removed you. If you are not, make sure that you add yourself as a user and add your public key to your .ssh/authorized_keys file. Otherwise, once you exit the staging node, you will not be able to get back on and someone will need to add your public key for you.