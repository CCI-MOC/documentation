## This page is deprecated. Please visit [this page](https://massopen.cloud/blog/wiki/kaizenfaqs/) instead.

Here are some solutions to common issues our users have:   
   
1. [DNS is slow / SSH is slow / `curl` is slow](#dns)    
2. [My VM cannot connect to the internet](#nointernet)

***

###<a name="dns"></a>DNS is slow, or SSH hangs partway for a minute or so before connecting

This happens because the system is waiting for an IPV6 timeout.  To fix it, add the following line to the end of /etc/resolv.conf:

     options single-request

If interested, more details are explained [at this link](http://udrepper.livejournal.com/20948.html).

***

###<a name="nointernet"></a>My VM cannot connect to the internet

Test whether your problem is actually an internet connection, or DNS.  From the VM, run the following commands:

    # ping www.google.com
    # ping 8.8.8.8

Successful responses look something like this:   
 
    64 bytes from lga15s47-in-f4.1e100.net (172.217.4.68): icmp_seq=2 ttl=53 time=25.6 ms


If the first command doesn't work, but the second one does, your problem is DNS.  Check the contents of /etc/resolv.conf. Does the line `nameserver 8.8.8.8` appear?  If not: go to Network-->Networks, click the relevant private network, and click Edit Subnet next to the relevant subnet.  Make sure `8.8.8.8` is entered in  the Nameservers box. and edit the relevant private subnet.  There are more detailed instructions on the [[Set Up a Private Network]] page of the tutorial.

If you can't ping 8.8.8.8, check that your security groups are allowing outgoing traffic. 
