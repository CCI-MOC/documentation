## Provisioning Nodes with Foremant
 1. Pick a node from free-pool. Lets say cisco-11.
```shell
[ukaynar@haas-master ~]$ haas list_free_nodes
```
 1. Add cisco-11 into moc-prod project
```shell
[ukaynar@haas-master ~]$ haas project_connect_node moc-prod cisco-11
```
 1. Attach moc-provision network to the cisco-11.
```shell
[ukaynar@haas-master ~]$ haas node_connect_network cisco-11 enp130s0f0 moc-provision vlan/native
```
 1. Now you have to do port forwarding to the node 39 and then you have to connect to the foreman VM 
 UI which runs on `http://10.13.37.1`.
 1. On the menu bar, click the "hosts", and then select the node you want to provision. 
 In our case, we should click the cisco-11. This will bring you the cisco-11 page and you will see a 
 "Build" button on the top-right of the screen. Click "Build".
 1. Give a reboot to the node by the following command.
```shell
[ukaynar@haas-master ~]$ haas node_power_cycle cisco-11
```
 1. Now you have to access to the IPMI console. Be sure you can access the IPMI console, 
 possibly as described in [VM-Setup-for-Cisco-IPMI-Access](VM-Setup-for-Cisco-IPMI-Access.html). 
 To access the IPMI console you have to do a port forwarding to the haas_master.
 Screen should be look like the following:
![](../_static/img/install_centos7_min_hd_step3.png)
 1. Start up the KVM console, and click "yes" and "continue" despite all the warnings. 
 There are extra instructions on this, depending on your browser, 
 in [VM Setup for Cisco IPMI Access](VM-Setup-for-Cisco-IPMI-Access.html). 
 1. Wait for machine to boot up, and make sure installation is completed.(Dont close the console we will need it again in the fiture)
 1. Detach the node from the moc-provision network and moc-prod project.
 1. Now you can add node in your project and attach network. Fors instance lets attach node to the "ceph-iscsi" project and "iscsi-net" network.
 1. Add cisco-11 into moc-prod project
```shell
[ukaynar@haas-master ~]$ haas project_connect_node ceph-iscsi cisco-11
```
 1. Attach moc-provision network to the cisco-11.
```shell
[ukaynar@haas-master ~]$ haas node_connect_network cisco-11 enp130s0f0 iscsi-net vlan/native
```
 1. If you would like to access outside world then you have to also attach "nat-public" network.
```shell
[ukaynar@haas-master ~]$ haas node_connect_network cisco-11 enp130s0f0 nat-public vlan/1053
```
 1. Give another reboot to the node by the following command.
```shell
[ukaynar@haas-master ~]$ haas node_power_cycle cisco-11
```
 1. Once the machine boot up, login and find the ip address of the machine.
 1. They you have to bring up the network interface 1053 
```shell
[ukaynar@haas-master ~]$  ifup /etc/sysconfig/network-scripts/ifcfg-enp130s0f0.1053
```
 1. If the machine has RHEL operating system, then we have to subscribe the node to the red hat portal 
 as described [here](Setting-up-external-network-on-RHEL7.1-nodes.html)
