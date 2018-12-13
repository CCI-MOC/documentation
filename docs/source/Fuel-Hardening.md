Fuel is terribly insecure out of the box; the root password is r00tme,
and all of its network services are exposed to the whole world -
including the dashboard, which requires no authentication at all. This
article details the ways in which we've remedied this, on both the MRI
hardware and our local test setup.

# iptables

We've adjusted the iptables configuration such that all incoming traffic
on the externally facing NIC is blocked unless:

- It is related to an existing connection (e.g. response to an http
  request we've initiated)
- It's tcp traffic coming in on the ssh port.

The diff is below. Note that this assumes `eth1` is the external nic.

    --- iptables.orig	2014-01-10 19:26:47.225178231 +0000
    +++ iptables	2014-01-10 19:32:32.834177546 +0000
    @@ -11,6 +11,9 @@
     :INPUT ACCEPT [0:0]
     :FORWARD ACCEPT [0:0]
     :OUTPUT ACCEPT [0:0]
    +-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT 
    +-A INPUT -m comment --comment "002 accept related established rules" -m state --state RELATED,ESTABLISHED -j ACCEPT 
    +-A INPUT -i eth1 -m state --state NEW -j DROP
     -A INPUT -p tcp -m state --state NEW -m tcp --dport 53 -j ACCEPT 
     -A INPUT -p udp -m multiport --ports 514 -m comment --comment "514 udp rsyslog" -j ACCEPT 
     -A INPUT -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT 
    @@ -25,10 +28,8 @@
     -A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT 
     -A INPUT -p udp -m state --state NEW -m udp --dport 53 -j ACCEPT 
     -A INPUT -p udp -m state --state NEW -m udp --dport 69 -j ACCEPT 
    --A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT 
     -A INPUT -p tcp -m state --state NEW -m tcp --dport 8080 -j ACCEPT 
     -A INPUT -p tcp -m state --state NEW -m tcp --dport 8000 -j ACCEPT 
    --A INPUT -m comment --comment "002 accept related established rules" -m state --state RELATED,ESTABLISHED -j ACCEPT 
     -A INPUT -p tcp -m state --state NEW -m tcp --dport 61613 -j ACCEPT 
     COMMIT
     # Completed on Thu Jan  9 23:36:12 2014

The MRI hardware has a few additional rules which are redundant. TODO:
we should clean these up.

# root password

We've changed the root password to our usual.
