# Kaizen Baremetal Accounts

This document covers how to get people setup with HIL and BMI.

## FreeIPA account

Users need to login the BMI host (`kaizenbm.massopen.cloud`) because:
* This host is connected to the provisioning VLAN, which means that this acts as the gateway for their baremetal nodes. (Optional: The VPN service can also connect users to the bmi provisioning vlan.)
* They run bmi commands from this host (BMI's client/server separation is screwed up).

1. Create a freeipa account for the user `$USERNAME`.
2. Add the user to the user group `kzn-frontend-users` on freeipa. This gives them access BMI host
3. On the BMI host login as the user so that it creates their home directoy. run `sudo su -l $USERNAME`.

## HIL and BMI account

1. Login to the BMI host. Make sure you have sudo permission on this host.

```
naved::~$ssh kaizenbm.massopen.cloud -l rado
Warning: remote port forwarding failed for listen port 52698
Last login: Thu Nov 21 14:41:29 2019 from 128.197.41.186
[rado@kzn-vbmi01res ~]$
```

2. Switch user to naved because his acocunt has passwordless sudo which is required for running the scripts. `sudo su -l naved`.
```
[rado@kzn-vbmi01res ~]$ sudo su -l naved
Last login: Thu Nov 21 14:41:33 EST 2019 on pts/2
You are naved
[naved@kzn-vbmi01res ~]$ ls bmi-automation/
create-account.sh  insert-key.sh
[naved@kzn-vbmi01res ~]$ ls becomeadmin
becomeadmin
```

3. `source ~/becomeadmin` to source HIL admin credentials.
```
[naved@kzn-vbmi01res ~]$ source becomeadmin
[naved@kzn-vbmi01res ~]$ cd bmi-automation/
[naved@kzn-vbmi01res bmi-automation]$
```

4. Run `./create-account.sh $PROJECTNAME $USERNAME`. $USERNAME must be the same as the FreeIPA username.
This script will:
* create a HIL and BMI account (project and user).
* put the HIL credentials in the users bashrc `/home/$USERNAME/.bashrc`.
* A message in their bashrc that tells them their project name.

##. BMI Images

1. Set `$KEY` environment variable to their ssh public key.
```
export KEY='ssh-rsa blabhalbh-blah...
```

2. Run `./insert-key.sh $PROJECT_NAME $IMAGE_NAME`
This script will:
* Import a bootable image into their bmi project.
* and then insert their key into it.

3. List of available images (value for $IMAGE_NAME)

* `rhel7-201811` - 25 Gigs
* `rhel7-201811-50G` - 50 Gigs

* `centos7-201811` - 25 Gigs
* `centos7-100G` - 100 Gigs

* `fedora30-201907` - 25 Gigs
* `fedora30-201907-100G` - 100 Gigs

* `ubuntu18-201811-100G` - 100 Gigs

By default give users the larger images (otherwise it's a pain to rbd resize their volumes later on)

## Tell users to read the manual.

https://docs.massopen.cloud/en/latest/clusters/kaizen/Kaizen-Bare-Metal.html