Openstack Kilo has puppet manifests through which ceph.conf could be created but we had to tweak in many things to make sure that it uses the conf file which can work.

We can find the puppet manifest files in /etc/puppet/environments/BigData/modules/quickstack. I tweaked in couple of files where I wanted them not to create ceph files. All the original files are backed up like:

compute_common.pp
controller_common.pp
neutron/controller.pp
neutron/compute.pp
params.pp

Also, we need to add interfaces for VLAN.250 on both controller/compute(for accessing storage, we can find them in network-scripts). Once the configurations are done, we can run puppet. For the first time, it failed for me and I had to recreate the nodes again :(. But when I ran it again, the puppet was able to update the required files on openstack nodes. This could be attributed to my lack of knowledge on ceph configuration parameters needed by openstack. So, as of now, I recommend the files to be modified manually and then once we have clarity on what parameters could be touched and what shouldn't be, we can automate the whole of stuff.

There were few problems on the ceph side which George can elaborate.(Like modifying the permissions etc).

So, if possible try modifying the following files manually on controller node. (Files can be found on compute-30,31 nodes in NEU cluster).
/etc/cinder/cinder.conf
/etc/glance/glance.conf

On Compute node:
/etc/nova/nova.conf

Add libvirt secret to connect to ceph:

novasecret.xml:
```
<secret ephemeral='no' private='no'> 
  <uuid>06d4bca6-46c2-4287-8f41-2daae26839db</uuid> 
  <usage type='ceph'> 
    <name>client.cinder secret</name> 
  </usage> 
</secret> 
```
```
[root@compute-29 ~]# virsh secret-define --file /tmp/novasecret.xml 
Secret 06d4bca6-46c2-4287-8f41-2daae26839db created

[root@compute-29 ~]# virsh secret-set-value --secret 06d4bca6-46c2-4287-8f41-2daae26839db --base64 AQDpCsFV0G/KExAAxmGu5J4V/NyFlzp4okAEvQ==
```

After that, try restarting services on both nodes.

Final Warning :P 

Make sure zeroconf is added in VLAN.250 or try removing it dynamically using ip route delete.


Once you create the volume/instance or upload a glance image, we can check
Checking if Ceph is working:
```
rbd --keyring /etc/ceph/client.production-openstack.key --id production-openstack ls production-ephemeral-vms
48fbafb5-259e-493b-912d-e0732d699cae_disk
```