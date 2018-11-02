We're currently using six machines at the MGHPCC as a production setup,
which we'll run our experiments on top of. Within the rack, they are
named node121 - node126.

# Logging in 
## To access Horizon/OpenStack
1. ssh -D 8000 mocuser@moc.mit.edu (note - you must have your ssh public key already added to the authorized_keys file)
 * If you are a customer, use your own username/password
 * Must set up SOCKS forwarding (ssh -D option) so that you can use the FUEL web interface with your web browser.
1. Browse to http://172.16.0.11

## Network settings
If you create networks, you'll need to use a router and the MIT DNS servers for the subnet if you want internet accessibility.

The MIT DNS servers are: 18.70.0.160 and 18.71.0.151

# To recreate the MOC environment
1. Click "new openstack environment"
1. Pick a name and openstack version.
 * Havana on centos is the version we've been using
1. Select Multi-Node
1. Select KVM (for bare-metal hardware deployment) or QEMU (if nested)
1. Select Neutron with GRE
1. Set storage to "default"
1. No additional services
1. Click "finish"
1. Select the environment you just created
1. Click "Add Nodes"
1. Assign the roles to the various machines
 * We've been using one node as a controller/cinder
 * The other node(s) are compute
1. Check the controller and all compute nodes. Select "configure interfaces"
 * Note - if some machines have different NIC configurations, you may have to configure those nodes separately 
 * Verify that eth0 and eth1 are green (meaning plugged in)
 * Drag Admin/PXE to the eth0
 * Drag the other 3 nets to eth1.
 * Click finish
1. Go to networks tab. Click "verify networks"
1. Click "deploy" (in top right corner)
 * This takes a while

# Hardware

The individual nodes each have the following hardware:

* 16 CPU cores
* 3.6 TB of logical disk space (We believe there is a hardware RAID
  underneath this)
* 64 GB of memory
* 4 Network interfaces

# Network

Our logical network layout looks as follows:

                           (public internet) 
                                   |
    <eofe1.mit.edu>-------+----<node121>-----+
                          |                  |
                          +----<node122>-----+
                          |                  |
           (IPMI network) +----<node123>-----+ (private network)
                          |                  |
                          +----<node124>-----+
                          |                  |
                          +----<node125>-----+
                          |                  |
                          +----<node126>-----+

## IPMI

`eofe1.mit.edu` is a head node which we can log into as mocuser. The
password is the usual, but we're mostly using ssh keys; if yours isn't
in there already, go ahead and add it (getting in initial via password).

It has access to the ipmi controllers for each of our machines, which
are resolvable as `node${node_number}.ipmi.cluster`. For each ipmi
controller there is a web interface and a cli. The cli is accessed via
ssh. The username and password are the same for both, if you don't have
them.

### Web Interface

The web interface includes a Java applet which can be used to view the
machines' text console. It appears to be using VNC under the hood, but
doesn't seem to work with a stock VNC client.

The applet has been a challenge to get working on some of our machines,
but it seems to work with the version of Firefox that is installed on
bungee.bu.edu (You need an active directory account to log in; ask Dan
if you don't have one). We've mostly had luck launching Firefox from a
VNC session (or X11 forwarding, but VNC is faster). Lately Firefox has
been failing to start for Ian, and we've been unable to diagnose the
problem thus far.

### CLI

The cli is accessible via ssh. There's a help command that can be used
to explore the available commands. One command of interest is the
following:

    reset /system1

Which reboots the node.


The following shell one-liner has been useful in rebooting all the nodes
(except for node121):

    for n in 2 3 4 5 6; do ssh root@node12${n}.ipmi.cluster reset /system1; done

This will require you to type the password once per node.


# Foreman Machines

            IP            Serial#

     moc0 - Foreman 

     moc1 - 172.16.2.132  45038Y1

     moc2 - 172.16.2.133  44B48Y1

     moc3 - 172.16.2.134  44W38Y1

     moc4 - 172.16.2.135  44938Y1

     moc5 - 172.16.2.136  44W28Y1

Each machine has 50GB /tmp named lv_tmp and 100GB /var named lv_var