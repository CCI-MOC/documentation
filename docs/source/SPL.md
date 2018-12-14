Here we describe What the SPL is and technical details

Relevant files, including SPL, can be found [HERE](https://github.com/CCI-MOC/moc-public/tree/master/python-mocutils/scripts)

# What it is / does

SPL is a tool for the dynamic editing of your cloud. There is a database of which machines are in the pool, their availability, which vlans are currently on the switch, which head-nodes exist, and their availability. Any of these fields can be pruned as necessary.

The user creates 'groups', consisting of a listing of machines from the pool, a head-node to control them, and a vlan for them all to communicate on.

Once the database is as desired, the user deploys changes. This logs into the switch, configures vlans as necessary, sends a list to each head-node of which machines it's been assigned, and finalizes changes to the database.

# Technical details

There is a sqlite db managed through an interactive python script. In the db there is a table for nodes (machines in the pool), vlans, head-nodes, and groups. On initialization of a new db, it is assumed these fields will be populated from files. From there, each command (create a new group, add machines to a group, etc) are just sqlite commands to edit the tables.

Once the db is configured how desired, the deploy command parses the state of the db, writes out the lists of machines to shared memory for vms to mount, sends the necessary commands to switch_controls.py to edit the switch's state, and finally calls vm-vlan to do the necessary vm management (create a bridge if necessary, attach the vm to the bridge, restart the vm)

SPL is now done. The rest is managed by each head-node individually.

# Post SPL

While this isn't specifically relevant to SPL, it was too short to put in it's own file.

After SPL restarts a VM to be a head-node, the VM has in /etc/rc.local to mount /etc/moc from death-star. This is the file containing the list of machines this head-node controls, it includes the mac address and management ip of each machine. It then runs [[linky.py|https://github.com/CCI-MOC/moc-public/blob/master/python-mocutils/scripts/linky.py]]. Linky will first create the symbolic links for the PXE booting of machines, explained more in the next wiki on PXE booting, and the power cycles the machines it controls.

# Using SPL

SPL is an interactive script. Run it passing the db location as an argument (if not it will prompt you for one, creating it if necessary). If a new db is created, it will ask you for the file containing a listing of machines in the pool, a list of initial vlans to create, and a file containing a listing of head-nodes. Each of these fields can be added to or pruned as necessary.

From there, type 'help' to see a list of commands.