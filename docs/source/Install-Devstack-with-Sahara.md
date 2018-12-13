# Install Devstack with Sahara
From Yue Zhang, June 2015

First of all, we should install devstack on a clean Ubuntu12.04 on Single Machine(Physical Machine/ VM). Installing devstack on a physical machine may **destroy the whole system**, you should be aware of it. 

**System Requirements**

* **Processor** - at least 2 cores
* **Memory** - at least 8GB
* **Hard Drive** - at least 60GB

```
sudo apt-get install git -y || sudo yum install -y git
git clone https://git.openstack.org/openstack-dev/devstack
cd devstack
```

### Configuration File

A `local.conf` file should be include into the devstack folder. 

```
[[local|localrc]]
ADMIN_PASSWORD=nova
MYSQL_PASSWORD=nova
RABBIT_PASSWORD=nova
SERVICE_PASSWORD=$ADMIN_PASSWORD
SERVICE_TOKEN=nova
```

### Enable Swift
```
enable_service s-proxy s-object s-container s-account
SWIFT_HASH=66a3d6b56c1f479c8b4e70ab5c2000f5
SWIFT_REPLICAS=1
SWIFT_DATA_DIR=$DEST/data
```

### Force checkout prerequisites

	FORCE_PREREQ=1

Keystone is now configured by default to use PKI as the token format which produces huge tokens. Set UUID as keystone token format which is much shorter and easier to work with.

	KEYSTONE_TOKEN_FORMAT=UUID

Change the `FLOATING_RANGE` to whatever IPs VM is working in.

In NAT mode it is subnet VMware Fusion provides, in bridged mode it is your local network. But only use the top end of the network by using a /27 and starting at the 224 octet.

	FLOATING_RANGE=xxx.xxx.xxx.224/27

Change the `HOST_IP` to your localhost or the IP of your VM

	HOST_IP=xxx.xxx.xxx.xxx

### Enable logging

	SCREEN_LOGDIR=$DEST/logs/screen

Set `OFFLINE` to `True` to configure `stack.sh` to run cleanly without Internet access. `stack.sh` must have been previously run with Internet access to install prerequisites and fetch repositories.

	OFFLINE=True

## Enable Sahara

```
enable_plugin sahara git://git.openstack.org/openstack/sahara
enable_plugin sahara-dashboard git://git.openstack.org/openstack/sahara-dashboard
```

### Install the devstack, Recover the Control Screen, Reload the devstack
To install the devstack, after creating the local.conf in the devstack folder, use the command. 

	./stack.sh

If you restart the VM, you could re-join the devstack by 

	./rejoin-stack.sh # feature removed March 2016

If you want to change the configuation file or restart the devstack, use these commands.

```
./unstack.sh
./clean.sh
./stack.sh
```

