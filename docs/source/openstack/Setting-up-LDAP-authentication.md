I'm not going to weigh in on LDAP vs. Kerberos. However here's how you can get LDAP working on the server and client ends.

first, assume we have a DNS of moc-ldap.bu.edu for the LDAP server. (it can be a VM on moc02, as long as it has an IP address reachable from all the clients)

client setup - Fedora / Centos - [to do - I've had it working on an FC13 machine at NU for a couple of years, but I'll have to see if the setup needs updating for FC20]

Client setup - Ubuntu
  `apt-get install libpam-ldapd`

It will ask a few questions.
  LDAP uri:  ldaps://moc-ldap.bu.edu
  verify cert never
  select passwd, group, and shadow for LDAP databases to use

finally run
  `service nscd restart`

Assuming the LDAP server is up, you can test with the 'id' command - 'id <user>' for a user in LDAP but not /etc/passwd should return the right data.

Server setup - this is a lot harder. The documentation I found was for Ubuntu, so that's what I tested it on.

1. install packages: `apt-get install slapd ldap-utils ldapscripts`
2. 'dpkg-reconfigure slapd'

At this point you'll be asked some questions; answers are:
- Omit OpenLDAP server configuration? *No*
- DNS Domain Name: *moc.bu.edu*
- Organization Name: *Mass Open Cloud* 
- Administrator Password: (choose one)
- Database backend: *HDB* (doesn't really matter)
- Do you want the database to be removed when slapd is purged? *No*
- Move old database? *Yes*
- Allow LDAPv2 protocol? *No*

Now we have to create the categories for our groups and users, because for some reason they're not built into the LDAP server. (taken from "Modifying/Populating your Database" from https://help.ubuntu.com/12.04/serverguide/openldap-server.html)

Create a file `foo.ldif` containing the lines:
`    dn: ou=People,dc=moc,dc=bu,dc=edu
    objectClass: organizationalUnit
    ou: People

    dn: ou=Groups,dc=moc,dc=bu,dc=edu
    objectClass: organizationalUnit
    ou: Groups`

Now run the command:
`ldapadd -x -W -D cn=admin,dc=moc,dc=bu,dc=edu -f foo.ldif`

We're ready to add users and groups - first configure ldapscripts, which makes it easier. Edit the file '/etc/ldapscripts/ldapscripts.config`, changing the following lines:
'SERVER="ldap://localhost"'
'SUFFIX=dc=moc,dc=bu,dc=edu'
'BINDDN="cn=admin,dc=moc,dc=bu,dc=edu"'

Edit the file '/etc/ldapscripts/ldapscripts.passwd' to contain the admin password **WITHOUT ANY NEWLINE**. You can use emacs, or use `echo -n passed > ldapscripts.passwd`

Now we can add a group or two:
`sudo ldapaddgroup mocusers`

and some users:
`sudo ldapadduser pjd mocusers
sudo ldapadduser okrieg mocusers`

To set a random password for a user:
`pjd@ubuntu-pjd:~$ ldappasswd -D cn=admin,dc=moc,dc=bu,dc=edu -W  uid=pjd,ou=People,dc=moc,dc=bu,dc=edu
Enter LDAP Password: 
New password: RcspWt12
pjd@ubuntu-pjd:~$`

or else you can add the -S flag and it will prompt you for the new password. Note that 'Enter LDAP Password' means the admin password.

Or if I wanted to change my own password:

  `ldappasswd -D cn=pjd,dc=moc,dc=bu,dc=edu -W -S`

and from another machine:

  `ldappasswd -H ldaps://ldap.moc.bu.edu -D cn=pjd,dc=moc,dc=bu,dc=edu -W -S`

in either case you will be prompted for your old password.

