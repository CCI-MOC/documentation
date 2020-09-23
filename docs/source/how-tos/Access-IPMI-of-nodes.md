## Access IPMI of Nodes

IPMI lets you remotely manage a server. It allows you to get a GUI physical console of your server, control power, mount an ISO to install an OS etc.

Follow these steps to access the IPMI (OBM) of nodes in any of our clusters.

## Method 1: Using x2go

1. Install x2go client on your local computer.
2. Launch x2go and add new session.
3. Enter the hostname, login, the appropriate authentication method (if not sure, check "Try auto login (via SSH Agent or default SSH key)").
4. Under session type use `xfce`. Click "OK" and then click on the session to connect.
5. You'll get a GUI from where you can launch firefox and navigate to the ipmi address of your server.
6. There you'll see something like "launch remote console" and that should be it.

## Method 2: SSH X forwarding.

1. `ssh username@remotehost -XY`
2. then run `firefox --no-remote`.

That will launch firefox and you can then navigate to the ipmi dashboard.

## Method 3: Using sshuttle and using your local browser.

I like this method the best because I get to use my local browser for navigating the IPMI dashboard which is much faster than other methods.

You could use SSH port forwarding (or remote port forwarding I think) with appropriate browser configuration (or you could setup foxyproxy for firefox) instead of sshuttle.

### Install sshuttle

Install [sshuttle](https://github.com/apenwarr/sshuttle#obtaining-sshuttle)

You can install it by either downloading from github or from PyPI using pip.

```shell
    pip install sshuttle
```

You can also install it with `pip3` if you have python3.

### Get javaws

Some servers require java to launch the console, but some work with HTML5 too. Try to launch the console with HTML5 first, if that doesn't work then proceed with installing javaws.

 -  On Ubuntu you could install icedtea:
```shell
    sudo apt-get install icedtea-netx -y
```
  and then from the command line you could execute `jnlp` files using `javaws file.jnlp`
  You can also install icedtea-plugin package to integrate with web browser.
```shell
    sudo apt-get install icedtea-plugin -y
```
 -  On CentOS, run:
```shell
    sudo yum install javaws -y
```

### Connect to the remote host with sshuttle

Open a terminal window execute this command (put in your remote host, duh)

```shell
   sshuttle -r username@remotehost --dns 10.0.0.0/19
```
 -  You could use alias from your ssh config files instead of specifying
```shell
    username@remotehost` in the command above.
```
 -  `-r` specifies the remote host.
 -  `--dns` will capture local DNS requests and forward to the remote DNS server.
 - '`10.0.0.0/19` will tunnel all traffic for that subnet via tha remote host. You can have multiple subnets forwarded. Our IPMI network is primarily on that subnet. OCT nodes are on `10.2.0.0/24`.
 -  `-N` tells it to ask the server which subnets it thinks we should route, and it route those automatically. This option seems to be broken, so I would not recommend this.

Minimize this terminal window and let it do its thing.

*Note: You won't be able to ping the ipmi address of the nodes even if you are connected with sshuttle. That traffic isn't forwarded.*

### Launch the  web browser on your local machine

Once connected, open your browser on your local machine and enter the ipmi address of the node you want to access. You should see the login page.

There's no need for any additional configuration of your web browser.

At this point, you can look at the bunch of stuff about the node. Since, every node's ipmi page is different, look around to find the right button to launch
the "remote console" (or something like that). 

That will most likely download a java file (`.jnlp`), which you can launch as described in the previous steps.
