# VM Setup for Cisco IPMI Access
The IPMI web GUI on the 48 Cisco nodes at NEU (as well as the IPMI for Fujitsu CD10K) must be accessed via SOCKS proxy, and relies on Flash and Java which are not well supported by all browsers.  

The best way to set up access is to configure a VM on your local machine with the correct plugins, with proxy forwarding set up for the 10.99.0.0/16 network.

* [Choose an OS for your VM](#choose-an-os-for-your-vm)
* [Install redsocks](#install-redsocks)
    * [Configure redsocks](#configure-redsocks)
    * [Configure iptables](#configure-iptables)
* [Install IcedTea](#install-icedtea)
* [Install flash plugin for Firefox](#install-flash-plugin-for-firefox)
* [Set up SSH key login on the VM](#set-up-ssh-key-login-on-the-vm)
* [Alternative setups](#some-alternative-setups)

If you encounter the SSL_ERROR_NO_CYPER_OVERLAP error while trying to connect, try [these steps](#ssl_no_cipher_overlap-error)

In a pinch, from a machine without a proxy utility, you can use [Firefox's IP settings](#using-firefox-proxy-settings) or try change your systemwide proxy settings.  Depending on how you set it up, some subset of the IPMI functionality may not be available, but you should be able to get a console.  If you change the browser ssl settings in about:config, make sure to change them back.

An [alternative setup with Chromium](#alternative-setup-with-chromium) is also described at below but has not been tested with recent versions of the OS, browser, etc.

******
### Choose an OS for your VM
The proxy utility [redsocks](http://darkk.net.ru/redsocks/) is only developed for Debian, Ubuntu, ArchLinux, and Gentoo - it is not supported for CentOS or RHEL.  

I have successfully tested these steps using [Lubuntu 14.04 LTS](https://help.ubuntu.com/community/Lubuntu/GetLubuntu) with Firefox 42.

Ian was previously able to get this working on ArchLinux (the cisco-flash-sandbox VM's on moc02).

******
### Install redsocks
Information on packages for the supported Linux flavors is available at [redsocks](http://darkk.net.ru/redsocks/).  On Lubuntu, you can install directly from the Ubunturepositories:

     $ sudo apt-get install redsocks


### Configure redsocks
Going forward I will use the following placeholders for arbitrarily chosen port numbers:

     <PROXY_PORT> - the port you use for port forwarding when connecting to the remote server, as in 
                    $ ssh -A -D <PROXY_PORT> user@haas-master

     <LOCAL_PORT> - an arbitrary unused port for redsocks to listen on

You can use any unused port you want, as long as you are consistent.  For extra security, it's a good idea not to use the defaults.

Open `/etc/redsocks.conf` and make the following changes:

In section `redsocks {}`:
     
     local_port = <LOCAL_PORT>
     
     ip = 127.0.0.1
     port = <PROXY_PORT>

In the section `redupd{}`:

     ip = 127.0.0.1
     port = <PROXY_PORT>
     dest_ip = 8.8.8.8

     // Comment out these lines:
     // login = username;
     // password = pazzw0rd;
    
     dest_ip = 8.8.8.8
     
*Working redsocks.conf example* : [sample_redsocks.conf](sample_redsocks.conf)    

This sample uses 12345 for `<LOCAL_PORT>` and 9000 for `<PROXY_PORT>` 

### Configure iptables
Add the following iptables rule:

     $ sudo iptables -t nat -A OUTPUT -p tcp -d 10.99.0.0/16 -j REDIRECT --to-ports <LOCAL_PORT>

This change to iptables will not persist after the VM reboots.  To make the change persistent, save the iptables rules:

     $ sudo sh -c "iptables-save > /etc/iptables.rules"

Then edit `/etc/network/interfaces` and add this line to the section under the interface your VM uses to connect to the outside world:

     pre-up iptables-restore < /etc/iptables.rules

On my Virtualbox guest running Lubuntu, the relevant interface was eth0, which did not have a configuration section yet, so I had to create one, like this:

      auto eth0
      iface eth0 inet dhcp
      pre-up iptables-restore < /etc/iptables.rules

There is more information about this step in [this guide](https://help.ubuntu.com/community/IptablesHowTo).

### Start the redsocks service

     $ sudo service redsocks start

The service should start automatically at boot going forward, but if you ever make changes to `/etc/redsocks.conf`, you will need to restart the service using 

     $ sudo service redsocks restart

******
### Install IcedTea
IcedTea is a browser plugin for running Java applets.  Install the latest version of it, so for example if the latest version is 8:

     $ sudo apt-get install icedtea-8-plugin 

******
### Install flash plugin for Firefox
Go to the [Adobe Flash Player Download Page](https://get.adobe.com/flashplayer/) and choose `.tar.gz for other Linux` from the dropdown menu.  Save the tarball to your hard drive and extract it.  Move the file `libflashplayer.so` into the Mozilla plugins folder:
     
     $ cd <directory-where-you-extracted-the-tarball>
     $ sudo cp libflashplayer.so /usr/lib/mozilla/plugins/

******
### Set up SSH key login on the VM
You need to enable the VM to log in to haas master using public key authentication. One method is to just (securely) copy your private key into this VM from your local machine.  

Or, you can generate a key specifically for this VM. You will have to append the new public key to `~/.ssh/authorized_keys` on the haas master. If you have an account on the emergency-recovery box, you will also want to add this public key there.

****** 
### SSL_NO_CIPHER_OVERLAP Error
If you see this error, you should be able to bypass it by disabling some SSL security settings in Firefox.  Open Firefox and type about:config into the URL bar.

You will get a popup warning about voiding your (nonexistent) warranty, click "I accept the risk" or "I'll be careful" or whatever the agree button says.

In the search bar, type: "security.tls.version" to filter the settings.  (Make note of what they were before the change so you can restore them if needed).

Change the setting **security.tls.version.fallback-limit** to 0
Change the setting **security.tls.version.min** to 0

Note that this makes your browser less secure, so don't use this VM to visit your bank website or anything.  If you do this on your real machine, make sure to change the settings back when you are done connecting to IPMI.

[Source](http://www.ryananddebi.com/2014/12/10/bypassing-the-ssl_error_no_cypher_overlap-error-in-firefox-34/)


******
### Some Alternative Setups 
 **Alternative Setup with Firefox proxy settings** 
It is possible to configure Firefox directly to use a SOCKS proxy. 

This is a less recommended approach than using redsocks, because certain parts of the IPMI interface will not launch in a way that uses the proxy, and therefore won't work. But if you just need to get to the KVM console, this will work.  

You will still need to install appropriate flash plugins and IcedTea as described above.

Next, open the Preferences page in Firefox.  Click Advanced on the left side, then Network on the page that opens.  Click "Setings" where it says "Connection: Control how Firefox connect to the Internet."

A dialog will pop up allowing you to configure a SOCKS proxy.

Choose manual configuration and set the following parameters:

     SOCKS host:  localhost
     Port: <PROXY_PORT>
     SOCKS version 5 

Now ssh to the haas master from the VM using the same port you specified.

     $ ssh -A -D <PROXY_PORT> <username>@129.10.3.48

You should now be able to reach the 10.99.1.x addresses in Firefox.

When you are done accessing the IPMI, remember to open the dialog and change it to "Automatically detect" or "No Proxy".

  **Alternative Setup with Chromium**

     $ sudo apt-get install chromium-browser
     $ sudo apt-get install pepperflashplugin-nonfree

Chromium doesn't work directly with IcedTea, so when you click "Launch KVM Console" you will need to save the .jlp file, then open it from your file browser (*not* fromthe bottom of the Chromium window) using IcedTea.  

The easiest approach is to set IcedTea to be the default program for this file type (but it still won't properly open straight from Chromium).

