* [Link to FreeIPA deployment recommendations](https://www.freeipa.org/page/Deployment_Recommendations)

# One IPA for all environments

FreeIPA server is running on the oVirt setup: freeipa.infra.massopen.cloud as experimental service for central management of user accounts and hosts enrolled.

The kerberos domain is: `infra.massopen.cloud`

## Policies

1. All users having admin access to all domain accounts must use two factor authentication/One Time Passwords (see above for registering an OTP token).

## Conventions for enrolling a host.

It's a good idea to stick to some conventions.

1. The hostname should specify the environment the host is for/in. If the host
 is for all environments, you can skip this.
 A hostname in kaizen should begin with `kaizen-` or `kzn-`. The latter is preffered,
 since its faster to type.

2. If you are registering a node, it would be a good idea to encode the location in the name.
 Example: `neu-3-21` tells us that the host is in Northeastern cluster, rack 3, unit 21.

3. Don't make the name too long either! `kaizen-hil-node-for-bmi-users-only`.

## Conventions for making sudo rules, HBAC rules, host groups or user groups.

Pretty much like how we name the hosts. Make sure to append the environment name
when making such entries.

## Client Enrollment

1. IMPORTANT: kerberos auth depends on time being in sync on client and server.
Run the following commands to sync time on boot.

```bash
echo "service ntpd stop && ntpdate freeipa.infra.massopen.cloud && service ntpd start" >> etc/rc.lical
chmod +x /etc/rc.d/rc.local
```

2. Install the FreeIPA packages

 * CentOS/RHEL: `yum install -y ipa-client`

3. Clients may be enrolled with `ipa-client-install --domain=infra.massopen.cloud --mkhomedir --enable-dns-updates`
 by a user who is part of the `enrollers` group.

 * --mkhomedir creates a new homedir for users who haven't logged into a machine before

4. **IMPORTANT STEP**

 Once the client is enrolled, password auth is enabled, we need to update pam.d config in
 `/etc/pam.d/sshd` config to disable that.

 Comment this line to disable password auth.

```
#auth       substack     password-auth
```

### Ubuntu bug
Ubuntu FreeIPA ignores the --mkhomedir option. To fix this, run:
```
echo 'session required pam_mkhomedir.so skel=/etc/skel/' >> /etc/pam.d/common-session
```
This bug has been reported [here](https://bugs.launchpad.net/ubuntu/+source/freeipa/+bug/1336869). An alternative fix may be found on [this webpage](https://www.redhat.com/archives/freeipa-users/2013-June/msg00091.html).

## Sudo on ipa hosts

To give sudo access on a machine:

* Make sure the HBAC rule allows `sudo`, `su-l`, `sudo-i` (or whatever you want)
 in addition to basic `sshd`. You could make a separate HBAC rule just for sudo.

* Then create a sudo rule to allow a user/user-group to run *any* command (there's only one)
 on the hosts/host-groups as anyone.

When you make changes to the sudo rule, you may have to delete the sssd database and files, and restart sssd.
```
rm -r /var/lib/sss/db/*
service sssd restart
```

# Registering an OTP

1. Download an application for your mobile device like FreeOTP or Duo. Duo is used by BU for BUworks.
2. Authenticate as an existing admin user using "kinit <username>"
3. Run: (if using FreeOTP) `ipa otptoken-add --owner=<user> --algo=sha512`
  * Otherwise (e.g. when using Duo), you can use the default sha1 (which is less secure) by running: `ipa otptoken-add --owner=<user>`


# Configuring replicates
When [enabling replicates](https://blog.vladionescu.com/freeipa-on-rhel-7-with-replication/), you will need to open up some ports in the firewall so the replicates can talk to each other. These include:

```
[from firewall-cmd --list-all-zones]
internal (active)
  interfaces: eth1
  sources:
  services: dhcpv6-client dns freeipa-ldap freeipa-ldaps freeipa-replication http https ipp-client kerberos kpasswd ldap ldaps mdns ntp samba-client ssh
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:
```

The following command does the proper replication, setting up DNS and the CA:

`ipa-replica-install --no-ssh --no-sshd --setup-dns --setup-ca --forwarder 128.197.253.183 --forwarder 128.197.253.120 --forwarder 128.197.253.254 --skip-conncheck  ~user/replica-info-ipa3.ipa.massopencloud.org.gpg`

## Disabling anonymous access
Anonymous access to the LDAP directory allows anyone with any access whatsoever to enumerate all of the objects (which includes info on users, hosts, and more).

To disable anonymous LDAP, each directory must have this run per the [fedora documentation](https://docs.fedoraproject.org/en-US/Fedora/18/html/FreeIPA_Guide/disabling-anon-binds.html):

```bash
ldapmodify -x -D "cn=Directory Manager" -W -h new_server_ip -p 389

Enter LDAP Password: <<password goes here>>
dn: cn=config
changetype: modify
replace: nsslapd-allow-anonymous-access
nsslapd-allow-anonymous-access: rootdse
<<hit ctrl-D>>
```
