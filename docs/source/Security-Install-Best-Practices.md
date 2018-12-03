# Security Install Best Practices
Follow these steps on all nodes exposed to the public internet.

### Do you really need to be public?
Please discuss things with Rado before putting a node on the public network. Many of our nodes don't need to be accessible in this way. 

If you just need internet access from your node, used the `nat-public` network described here: [Accessing-Northeastern-Cluster](Accessing-Northeastern-Cluster.html)

### Use Strong Passwords
Some methods for generating passwords randomly:

**Random String**

    $ cat /dev/urandom | base64 | dd count=14 bs=1
    pFt0wCQUFKTL4c14+0 records in

Adjust "count" to change the length of the password, and remove the `<count>+0 records in` from the end of the output. 

**Random dictionary words**

    $ shuf -n2 /usr/share/dict/words

Replace '2' with the number words you want to generate (but always use at least 2).

A slower alternative if your system doesn't have `shuf`:
    
    $ cat /usr/share/dict/words | sort -R | head -n 2

An alternative that works on Mac OSX. It only generates one word at a time:

     $ head -$(jot -r 1 1 $(cat /usr/share/dict/words | wc -l)) /usr/share/dict/words | tail -1

Also, keep the passwords a secret!  Don't send passwords via unencrypted email, post them to #MOC, or put them in a public git repository.

## Log in as yourself, not root.
Create a user account with your own name, and sudo when you need root permissions.  This allows disabling remote root login (see below), and also makes it easier to see who is doing what, or fix things if someone's key is compromised.


## SSH

### Add public SSH keys to user accounts
You will need root privileges to do this for any user that is not yourself.

    # mkdir /home/lihua/.ssh                        // Create .ssh directory in the user's home folder
    # vim /home/lihua/.ssh/authorized_keys          // Copy the user's public key to this file
    # chown -R lihua:lihua /home/lihua/.ssh         // Change owner:group of .ssh/ and its contents to the user
    # chmod 700 /home/lihua/.ssh                    // Set permissions on .ssh directory
    # chmod 600 /home/lihua/.ssh/authorized_keys    // Set permissions on authorized_keys file

It is important to set the permissions correctly, otherwise key authentication will not work.  To double check, type:

    # ls -al /home/lihua/.ssh/

The output should look like this (known_hosts may or may not be there):

    drwx------. 2 lihua lihua   46 Aug 13 09:41 .
    drwx------. 3 lihua lihua 4096 Aug 13 12:09 ..
    -rw-------. 1 lihua lihua  741 Aug 13 06:19 authorized_keys
    -rw-r--r--. 1 lihua lihua  173 Aug 13 09:41 known_hosts   // Don't worry if this file isn't there


### Disable password authentication and remote root login
**Important:**  Make sure to set up your own account with public key authentication **and check that it works** before making these changes.  

Edit the file `/etc/ssh/sshd_config`. Make sure the following settings are set to `no`:

    PermitRootLogin no
    PasswordAuthentication no
    ChallengeResponseAuthentication no
    # Sometimes reverse lookups cause problems
    UseDNS no

Then restart sshd:

    # systemctl restart sshd

Before logging out, make sure to test that ssh works by logging in from a separate shell, or typing:

    $ ssh localhost

Once this step is complete, an admin will have to create accounts and add keys for any new users who need to log into the machine.

### Disable remote lookups for connections
In order to prevent the wasting of resources as well as [lookup vulnerabilities](http://arstechnica.com/security/2016/02/extremely-severe-bug-leaves-dizzying-number-of-apps-and-devices-vulnerable/), include in sshd_config:

```
UseDNS no
```

### Enable sshguard or something similar
sshguard is available on Ubuntu by running `apt-get install sshguard` and prevents automated brute-force attacks that can be used to attack passwords as well as vunerabilities such as defeating ASLR.

## Enable the firewall
* For Ubuntu, this can be done using `ufw enable`.
* For RHEL/CentOS, one can use `system-config-firewall`

## Set up NTP
Can Chrony/OpenNTPD as an NTP client. Good timestamps will help debugging problems later.

Ubuntu users should make use of openntpd, as it has built-in privilege
separation and other security benefits. To do so: `apt-get install openntpd`. Its config file is `/etc/openntpd/ntpd.conf`.

CentOS/RHEL users should use chrony:
* `yum install chrony`
* Edit `/etc/chrony.conf`, replacing the "server" lines with your local servers.

BU machines can use ntp{1,2,3}.bu.edu as their time server. This can be done by replacing the `server` lines with 
these:
```
server ntp1.bu.edu
server ntp2.bu.edu
server ntp3.bu.edu
server ipa1.ipa.massopencloud.org
```

MIT has time.mit.edu.


## Disable IPv6
We don't use IPv6 for anything. Also, many firewalls don't protect against it
by default, effectively meaning there is no firewall if it is enabled.

For CentOS, RHEL and Ubuntu, add this to /etc/sysctl.conf:
```
net.ipv6.conf.all.disable_ipv6 = 1
```

Also, one can run `sysctl -w net.ipv6.conf.all.disable_ipv6=1` to make the
setting active on the current machine.

### Enable automatic updates

**For CentOS/RHEL** :
* `yum install yum-cron`
* Edit /etc/yum/yum-cron.conf with these in mind:
 * update_cmd can be set to "security" in order to install only security updates automatically
 * `apply_updates` must be set to `yes`
* On RHEL, you must enable the yum-cron service by running `systemctl enable yum-cron` and `systemctl start yum-cron`.
 * CentOS 7 uses cron and so shouldn't need the service

**For Ubuntu**:
* `apt-get install unattended-upgrades`
* Edit `/etc/apt/apt.conf.d/50unattended-upgrades` with these in mind:
 * Uncomment the updates you want to automatically install (at least `-security`)
 * Enable `Automatic-Reboot` so that kernel security packages take effect
 * Optionally set an `Automatic-Reboot-Time` that is more to your liking that "now"

## Hidepid
If the system has multiple users logging in who maybe don't trust each other completely (like a gateway system), it might be good to set hidepid, which prevents users from gathering info on other users' processes.

To do this, follow [this tutorial](https://www.cyberciti.biz/faq/linux-hide-processes-from-other-users/) on adding hidepid to the proc flags in `/etc/fstab`.

