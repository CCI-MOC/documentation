The PDUs used at MGHPCC have an Ethernet port and can be configured for remote access.  One of the useful things this allows is disabling specific power ports when doing remote maintenance (such as a firmware upgrade) that requires the machine to be actually "unplugged" for a period of time.

Currently, only the PDUs in the Kilo cabinet (R4-PA-C4) are connected to the management network and configured for remote access.  The PDUs in C2 will also be configured once there are switch ports available for them.

* [Layout and Labeling Conventions](#pdu-layout-and-labeling-conventions) 
* [Local Configuration](#local-configuration)
* [Remote Configuration](#remote-configuration)
* [Resetting the PDU](#resetting-the-pdu)
* [More Info](#more-info)

#### PDU Layout and Labeling Conventions

Each rack has two PDUs, which are identified as **PDUL** and **PDUR**.  PDUL is to the left and PDUR is to the right as viewed from the hot aisle (inside the pod).

Each PDU has 3 "phases".  You can read more about 3-phase power [at Wikipedia](https://en.wikipedia.org/wiki/Three-phase_electric_power) but the important point is that it is a good idea to spread the rack's load out across the 3 phases when possible.  

You should also make sure that any equipment with redundant power supplies is plugged into two different PDUs, not twice into the same PDU.  It's best to also plug into different phases - for example, L1 on PDUL, and L2 on PDUR.  It's possible for a lightning strike or similar event to knock out only one phase at a time.

The phases are labeled **L1**, **L2**, and **L3**, laid out from top to bottom:

       (top of rack)
            L1

            L2 

            L3 
      (bottom of rack)

Each phase has 16 C13 outlets in two vertical rows, which are numbered like this:

       (top of phase)
          P1   P9
          P2   P10
          P3   P11
          P4   P12
          P5   P13
          P6   P14
          P7   P15
          P8   P16
      (bottom of phase)

These numbers are assigned by the manufacturer. On the PDU the number appears above each outlet in very tiny font.  We have added the "P" on the labels to clarify that we are talking about the power outlet.  Our labeling convention uses "P" (for "power outlet" or "port", take your pick) instead of "O" (for outlet) to avoid any possible confusion with the number "0".

Note that because the PDU's are facing in opposite directions, differently-numbered ports are located "close" to the rack vs. "far" from the rack on the left and right.  For example, PDUL-L1-P9 and PDUR-L1-P1 are located symmetrically "opposite" each other, closer to the servers than PDUL-L1-P1 and PDUR-L1-P9.

Each power plug should have a label like this identifying the server, power supply on the server, and the PDU, phase, and port it plugs into.

     ceph-lenovo01 PS1
     PDUL-L1-P3

***

#### Local Configuration

The PDU's default settings are:

     username: admn
     password: admn
     IP:  192.168.1.254

Set up an interface on a laptop or local server in the same subnet as the PDU, and connect it to the Ethernet port.
    
    ifconfig <interface-name> 192.168.1.1 netmask 255.255.255.0

Use telnet (not SSH) to log into the PDU using the default account:

    telnet 192.168.1.254
    username: admn
    password: admn

On successful login, you should see the 'Switched CDU:' prompt.  

Execute the following commands to change the network configuration.  In the example, we add the PDU to the Engage1 management network (10.10.10.0/24) and give it an IP address of 10.10.10.34.

    set dhcp disabled
    set gateway 10.10.10.1
    set ipaddress 10.10.10.34

You should see a notice that a restart is required to take effect.  Don't do it yet.

Next, create a non-default user:

    create user pduadmin

Enter the user's password when prompted.  Please set (and document) an actual secure password, not PASSWORD.  (For tips on creating secure passwords, see [[Security Best Practices]]).

    Password: PASSWORD
    Verify:   PASSWORD

Now make the new user an admin user:
  
     set user access admin pduadmin

Restart the PDU to let your settings take effect (you will be logged out):
    
     restart

The new network settings will take effect, and you will no longer be able to access the PDU from 192.168.1.1.  Go ahead and connect the PDU to the management network, making sure to configure the switch port for the correct VLAN.  (If this isn't possible, you can also give your laptop an address on 10.10.10.0/24 for now and continue.)

Log in as the new user:

    telnet 10.10.10.34
    username: pduadmin
    password: PASSWORD

Remove the default account:

    remove user admn

The PDU is now configured for remote access.

***

#### Remote Configuration

You can do the above steps remotely, even if the PDU's IP is set to the default so it is outside your network config.
* Create a temporary VLAN for this network on the management switch.
* Configure a tagged interface on another node (such as a services node).
* Configure the switch ports for that node and the PDU to use the temporary VLAN.
* From the node with the tagged interface, configure the PDU as described above.
* Change the VLAN assigned to the PDU's switch port to the IPMI network VLAN. 
* Remove the temporary vlan you created from the services node switch port, and delete the temporary VLAN.
* Delete the tagged interface from the services node.

***

#### Resetting the PDU

To reset the PDU to factory defaults, locate the reset button (it is tiny and located near the Ethernet port).  Push and hold the button for 10 seconds.  Note that this resets the entire PDU configuration, but does not interrupt the power flow, so it is safe to do this while things are plugged in.

You will need a small narrow tool to push the button.  It is recommended to use something that is NOT conductive (so don't use a metal paperclip).

***

#### More info

* [Manual](301-0429-1_SWITCHED_POPS_CDU_v6-1_Sept12.pdf)<br>*Note: the units pictured in the manual are a different model, but the same document also covers our model which is CWG-48VD.*
* [Manufacturer's product page](https://www.servertech.com/products/pops-per-outlet-power-sensing/pops-switched-cwg-48vd-vy-3ph-20-30a-with-pips)
* MOC owns a 3-prong to C13 adapter which is useful for plugging in your laptop while working inside the datacenter.  It is located in the red crate stored at MGHPCC.