Follow these steps to access the IPMI (OBM) of nodes in any of our clusters.

### Get javaws

You need java web start to launch the remote console.

* On Ubuntu you could install icedtea:

    `sudo apt-get install icedtea-netx -y`

and then from the command line you could execute `jnlp` files using `javaws file.jnlp`
You can also install icedtea-plugin package to integrate with web browser.

    `sudo apt-get install icedtea-plugin -y`

* On CentOS, run:

    `sudo yum install javaws -y`

### Install sshuttle

Install [sshuttle](https://github.com/apenwarr/sshuttle#obtaining-sshuttle)

You can install it by either downloading from github or from PyPI using pip.

`pip install sshuttle`

### Connect to the remote host with sshuttle

Open a terminal window execute this command (put in your remote host, duh)

`sshuttle -r username@remotehost -N --dns`

* You could use alias from your ssh config files instead of specifying
`username@remotehost` in the command above.
* `-r` specifies the remote host.
* `-N-` tells it to ask the server which subnets it thinks we should route, and
    it route those automatically.
* `--dns` will capture local DNS requests and forward to the remote DNS server.

Minimize this terminal window and let it do it's thing.

Note: You won't be able to ping the ipmi address of the nodes even if you are
connected with sshuttle. That traffic isn't forwarded.

### Launch the  web browser on your local machine

Once connected, open your browser on your local machine and enter the ipmi
address of the node you want to access. You should see the login page.

There's no need for any additional configuration of your web browser.

At this point, you can look at the bunch of stuff about the node. Since, every
node's ipmi page is different, look around to find the right button to launch
the "remote console" (or something like that) that will most likely download
a java file (`.jnlp`), which you can launch as described in the previous steps.


