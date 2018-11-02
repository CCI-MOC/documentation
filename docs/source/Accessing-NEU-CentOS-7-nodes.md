## Accessing the CentOS 7 nodes

Note: this page is intentionally not linked to by anything until we get public key authentication working!

The two CentOS 7 nodes are cisco-04 and cisco-09. I have set up static IP addresses for these two nodes, which can be used to ssh into the nodes from the HaaS master (129.10.3.48).

cisco-04 can be accessed from the haas master via 'ssh user@172.16.0.104'.
cisco-09 can be accessed from the haas master via 'ssh user@172.16.0.109'.

The username/password for both nodes is user/hpctest7

The root password is also hpctest7

On cisco-09, the username "user" has sudo access. Due to a mistake on installing on cisco-04, the username "user" does not have sudo access, so you'll have to run "su" and log in as root to do sudo tasks. Anyone is welcome to give "user" sudo access on cisco-04, I just haven't had a chance yet.

There is also one RHEL node, cisco-03, which has the static IP 172.16.0.103, and can be logged into via user/huytest7. 

