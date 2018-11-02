# AMT BIOS

The AMT BIOS on our Optiplex machines allows for remote power-cycling, which is critical for using them with the HaaS. Currently, Iron and Cesium are part of the HaaS cluster, and the AMT BIOS is set up as follows:

* Small business mode
* password is the MOC AMT BIOS Password
* Hostnames are (it's not clear that these matter in any way, but...):
  * Iron: `amt-iron`
  * Cesium: `amt-cesium`
* BIOS ip addresses are:
  * Iron: `192.168.128.5`
  * Cesium: `192.168.128.6`
* Other network settings:
  * Netmask: `255.255.255.0`
  * Gateway/DNS: `192.168.128.1`
  * Domain name: `moc.bu.edu`, which is both arbitrary and unresolvable. As far as we know, we don't need this value.

At least on the 755's (the smaller Optiplexes from Northeastern), one can reset the machines to factory defaults by removing the CMOS battery, which will set the password to the Default AMT BIOS Password. The BIOS will insist that you change this before configuring anything else, and has some requirements for the new password, including:

* At least 8 characters
* Both uppercase and lower case letters
* At least one number
* At least one symbol
* Certain characters don't count (e.g. spaces).

The current AMT configuration of the other machines is unknown; when the AMT BIOS is needed, they will have to be reset.

# Standard BIOS

The machines in the HaaS cluster need to have PXE set as their first boot option, and virtualization extension turned on. On at least the 755's, this is not the default. To fix this, press F2 to enter setup, and change the following:

* Onboard Devices -> Integrated NIC -> On w/ PXE
  * If you look at System -> Boot Sequence, it will have netbooting set as the first option, but say "not present". The above is needed to remedy this, though no change needs to be made in the boot sequence. Note that even after enabling PXE, the boot sequence may still say not present.
* Performance -> Virtualization -> turn the virtualization extensions on.