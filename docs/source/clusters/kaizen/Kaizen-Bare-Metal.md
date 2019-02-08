# Kaizen Bare Metal

Please email kaizenbm@lists.massopen.cloud if you wish to schedule nodes for a longer
duration.

## SSH to kaizen machines.

`ssh username@kaizenbm.massopen.cloud`

## How to provision nodes using HIL and BMI

1. See a list of free nodes

```
hil node list free
```

2. Add nodes to your project from the free pool.

```
hil project node add <project> <node>
```

Repeat this command to add more nodes to your project.

3. Enable OBM on your nodes so you can power cycle, power off, or view node's serial console.

```
hil node enable obm <node>
```

4. Connect the node the BMI provisioning network.

```
hil node network connect <node> nic1 bmi-pro vlan/native
```

5. See list of images available in BMI.

```
bmi ls <project>
```

6. Now provision the node using BMI

```
bmi pro <project> <node> <image> nic1
```

7. Power cycle the node and wait for 3-4 minutes

```
hil node power cycle <node>
```

8. SSH to the node from the kzn-bmi host, or from your local machine if you have VPN access.

for a node named `neu-X-Y` the IP wil be `10.255.X.Y`

```
ssh 10.255.X.Y -l root
```

9. Put your ssh key and *disable password* login over ssh.

## How to deprovision a node and return it to free pool.

1. Power off the node

```
hil node power off <node>
```

2. Deprovision the nodes using BMI.

```
bmi dpro <project> <node> <nic>
```

This command is destructive and *will* delete your image.

3. Remove the bmi provisioning network (and any other networks).

```
hil node network detach <node> nic1 bmi-pro
```

4. Disable OBM

```
hil node obm disable <node>
```

5. Remove node from your project

```
hil project node remove <project> <node>
```


## Important notes
 -  Each node has 2 nics labelled nic1 and nic2.
 -  Currently the nodes don't have a lease time, but a leasing system will be added soon.
 -  For provisioning, we connect `nic1` to the bmi provisioning network natively using HIL.
 -  If you require a public IP address for your node, talk to Naved/Rado.

## Using HIL from your local machine

If you have pip (or in python virutalenv), run

```
pip install git+https://github.com/cci-moc/hil
export HIL_ENPOINT=https://kzn-hil.massopen.cloud
```

Export your HIL credentials that an admin should have given you. In bash, do this

```
export HIL_USERNAME=username
export HIL_PASSWORD=password
```

You could put these in your bashrc so they are automatically in your environment
when you login (you could figure out it's equivalent if you are using a different shell).

### More HIL commands

 -  `hil project node list <project-name>`
  This will list all the nodes that are currently added to your project.
 -  `hil node list free`
  will list all nodes that are available.
 -  `hil project node add <project-name> <node-name>`.
  This will add the node to your project.
 -  `hil project node remove <project-name> <node-name>`.
  This will remove the node from your project. Before running this command, make
  sure that you remove all networks first and disable OBM.
 -  `hil node show <node-name>`
  This will show you the information about your node.
 -  `hil node network connect <node-name> <nic-name> <network-name> <channel>`
  to connect your node's nic to a network. Channel could either be `vlan/native`
  or `vlan/<vlan-id>`.
 -  to find the vlan-id of a network, run `hil network show <network-name>`
 -  `hil node network detach <node-name> <nic-name> <network-name>`
  this will remove <network-name> from your node's nic.
 -  `hil node obm enable <node-name>` to enable OBM which lets your perform all
  power commands, and view console.
 -  `hil node obm disable <node-name>` to disable OBM.
 -  `hil node power cycle <node-name>`.
  to power cycle a node.
 -  `hil node power off <node-name>`.
  to power off a node.

For more information about HIL, checkout the [HIL Repo](http://github.com/cci-moc/hil)

## More BMI Commands

To snapshot your node, run
```
bmi snap create <project> <node> <snapname>
```

You can now provision more nodes from this snapshot. Just provide the <snapname> as
the image name

