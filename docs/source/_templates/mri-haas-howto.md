This document outlines the configuration of the HaaS master on the MRI
hardware. It also describes how to use this setup.

# Logging in

The MRI HaaS master has an ssh daemon listing on port 2222. It accepts
public-key authentication only. If you don't already have a set of
credentials, talk to Ian about getting one. The machine's ip address is
18.13.52.71. Once you have your ssh keys set up, you should be able to
log in via:

    ssh -p2222 you@18.13.52.71

You'll need to set a password first though, so you can sudo. Because you
don't have an existing password, you can't do this as yourself. Your key
should also work for the ansible user, which can sudo without a
password, so to get yourself going, you can do:

    ssh -p2222 ansible@18.13.52.71
    # Once logged in:
    sudo passwd you

Then log out and log back in as yourself.

# Setting up and running the HaaS

From there, you should clone a copy of the HaaS git repository, and cd
into the resulting directory:

    git clone git://github.com/cci-moc/haas
    cd haas

The default branch (`devel`) is current - if you're working from a fresh
clone you don't need to do anything to get the up to date mainline, but
if you've got an old repo that you're updating, you'll need to switch
branches:

    git checkout devel

You'll need a config file for the HaaS with the appropriate settings.
There is one available in `/home`:

    cp /home/haas.cfg ./

You need to set up a python virtual environment with all of the
dependencies of the HaaS:

    virtualenv .venv
    source .venv/bin/activate
    pip install -e .

You then need to create the database (and tables):

    haas init_db

The next step is to start the HaaS server, which must be run as root:

    sudo haas serve

Before doing anything else, you need to populate the database with
information about available hardware. There is a script at
`/home/dbinit.py` which will do so with the right information for the
mri hardware:

    python /home/dbinit.py

Now you're ready to to start wiring up a group/project/partition. The
`haas` command line tool provides an interface to this. An example
session might be:

    # create a user and group. credentials aren't checked currently.
    haas user_create alice secret
    haas group_create group1
    haas group_add_user group1 alice

    # create a project, associated with our group
    haas project_create proj group1

    # create a headnode
    haas headnode_create hn-1 proj

    # create some networks
    haas network_create net-ipmi proj
    haas network_create net-pxe proj

    # reserve some nodes for our project
    haas project_connect_node proj 195
    haas project_connect_node proj 196
    # ...

    # add some nics to our headnode
    haas headnode_create_hnic hn-1 hn-ipmi de:ad:be:ef:20:14
    haas headnode_create_hnic hn-1 hn-pxe de:ad:be:ef:20:15
   
    # connect our nodes to our networks 
    haas node_connect_network 195 node-195-ipmi net-ipmi
    haas node_connect_network 195 node-195-nic1 net-pxe
    haas node_connect_network 196 node-196-ipmi net-ipmi
    # ...

    # connect our headnode to our networks
    haas headnode_connect_network hn-1 hn-ipmi net-ipmi
    # ...

    # Go!
    haas project_apply proj

The provided headnode expects two networks, an ipmi network, and
a pxe network. The nodes are set to pxe boot off of what is called
`node-N-nic1`. The above example demonstrates a way to achieve the
correct topology.

`project_apply` will go off and create some network interfaces, create
and the vm, and configure the switch appropriately. There's currently
no automated way to clean up the network state, so if you want to
free up vlans to be used with new networks, you'll have to follow
the instructions in `doc/network-teardown.md` in the HaaS source tree.

# Using the headnode

Once the group is deployed, the headnode will be up and running sshd.
you can log in as `mocuser`, with the usual password. If you don't know
the password, ask someone. You'll need to know the ip address of the
machine. Right now the way to do this is just nmap the network, and try
to guess which of the machines listening on port 22 is yours:

    nmap 192.168.122.0/24

The `uptime` command is a decent way of validating that the machine
you've logged into is a fresh one.

The headnode is running Ubuntu 14.04. It has several services running:
DHCP and TFTP, which together allow for PXE booting machines, a web
server (nginx), which on port 80 has it's document root pointed at
the same directory as tftpd (/var/lib/tftboot), and has port 8080
set up as a proxy for a helper script, see below. The machine hands
out ip addresses on the 192.168.10.0/24 subnet, and is configured to
NAT this subnet, properly routing packets bound for the outside.

ipmitool is installed on the headnode. Some example commands:

    # Reboot a machine:
    ipmitool -H <ip address> -U root -P calvin chassis power cycle
    # Set the first boot device to PXE, for the next boot only:
    ipmitool -H <ip address> -U root -P calvin chassis bootdev pxe

The formula for a node's ipmi ip address is:

    172.16.2.<node id + 10>

The default config file for pxelinux (the bootloader) simply chainloads
to the disk. There is an alternate config file at
`/var/lib/tftpboot/centos/pxelinux.cfg`, which starts a kickstart
installation of CentOS 6.5.

General procedure for deploying CentOS:

1. populate `/home/mocuser/macs` with the mac addresses of the first nic
   on the nodes of interest. (Note: this would be `nic1`, *not* the
   ipmi controller.) letters in the mac addresses should be lower-case;
   this seems to be what pxelinux expects. The file initially contains
   the mac addresses for nodes 195 and 196.
2. create some symlinks, which will cause the appropriate nodes to boot
   into the installer when power cycled:

    cd /var/lib/tftpboot
    ./make-links.sh

   The presence of a mac-address specific config file overrides the
   default, so by making this a symlink to the centos config, we can
   re-image specific nodes.
3. Make sure the boot_notify.py script is running. It probably makes
   sense to start this in a screen session:

    screen
    sudo python boot_notify.py
    # Do C-a d to detach

   This is a small web-server that listens for notifications from
   CentOS' post-install script, deleting the appropriate symlink as
   created in step 2. This has the effect of reverting those machines to
   chainloading to the disk, thereby booting into their newly installed
   OS.
4. Set up the boot devices and power cycle the nodes:

    ipmitool -H <ip address> -U root -P calvin chassis bootdev pxe
    ipmitool -H <ip address> -U root -P calvin chassis power cycle

   The first step may not be totally necessary, but this guarantees that
   the machine will pxe boot as expected.

The machines should go ahead and install CentOS, deleting their symlinks
when finished, and then rebooting into the installed OS. You can log
into them via ssh, as user `root` with password `r00tme`. The install
typically takes 30-45 minutes.
