## Specifications
Data sheet can be found [here](https://www.google.com/webhp?sourceid=chrome-instant&rlz=1C5CHFA_enTH540US541&ion=1&espv=2&ie=UTF-8#q=ucs+c220+m3+sff+datasheet)

The stock disks on these nodes have a firmware issue that causes problems with disk partitioning.  See [[Firmware Issue with Cisco 300GB Drive]] for an explanation and workaround.

## IPMI access

See also [[VM Setup for Cisco IPMI Access]]

The Cisco IPMI interfaces only support SSLv3, which is disabled by default in newer firefox versions.

To enable it, follow the instructions from [here](http://www.ryananddebi.com/2014/12/10/bypassing-the-ssl_error_no_cypher_overlap-error-in-firefox-34/).

 The meat is to change these in `about:config`::

First, right-click on the setting “security.tls.version.fallback-limit” and select modify.  You’re going to change the “1” to “0”.  Then do the same thing with “security.tls.version.min”, changing the “1” to “0”.

##Enabling the Management Port

The Cisco UCS C220 has a dedicated management port and two in-band ports (LOM).  The default settings allow any LOM port to be used to access the IPMI, and the dedicated management port is disabled by default.  

We use the dedicated management port for these systems, so it will need to be re-enabled if the machines ever get restored to factory defaults.

**WARNING**: *This is not something you want to try remotely - it involves cutting yourself off from the server until you move the cable to the management port.  So * ONLY * do this if you have physical access to the server!*

To enable access via the management port:

Connect the Ethernet cable to one of the two LOM ports
SSH into the OBM and enter the following commands:

    scope cimc
    scope network
    set redundancy none

You will see a warning that the mode is not compatible.  This is OK - you are about to change the mode.

    set mode dedicated
    commit

You will see a warning that you might lose your connection, do you want to continue (y/n)?

    y

The connection will be lost because you've disabled the LOM port.  Go move the cable to the management port.  You should now be able to establish a connection.

Source:
[Cisco UCS C-Series CL Configuration Guide: Server NIC Configuration ](http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/c/sw/cli/config/guide/2-0/b_Cisco_UCS_C-Series_CLI_Configuration_Guide_201/b_Cisco_UCS_C-Series_CLI_Configuration_Guide_201_chapter_01000.html#d43507e44a1635)