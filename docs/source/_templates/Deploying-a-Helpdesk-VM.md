### VM Setup

Launch a CentOS 7 VM, flavor m1.small (20GB disk, 1 cpu, 2GB RAM). 

Do the following housekeeping:

     $ sudo yum install epel-release 
     $ sudo yum install git python-pip gcc python-devel
     $ sudo yum update
     $ sudo reboot

It is also recommended to install and set up yum-cron for security updates.

Create the relevant users:

     $ sudo useradd -d /usr/local/src/moc-tools moc-tools
     $ sudo useradd helpdesk

***

### Set up the moc-openstack-tools repo

     $ sudo su - moc-tools
     $ pwd
     /usr/local/src/moc-tools
     $ git clone https://github.com/CCI-MOC/moc-openstack-tools production
     $ cd production
     $ cp example_settings.ini settings.ini

You also need to install dependencies from the requirements.txt file as root:

as centos user:
     $ sudo su -
     # cd ~moc-tools/production
     # pip install -r requirements.txt

(If there are pip errors, make sure `gcc` and `python-devel` are installed, and sometimes `pip install -U pip` and `pip install -U setuptools` help.)

***

### Mailman setup

The moc-tools user needs SSH access to the `mail.massopen.cloud mail` server in order to subscribe new users.

    $ sudo su - moc-tools
    $ mkdir .ssh
    $ chmod 700 .ssh
    $ ssh-keygen -b 4096 -t rsa

There is also a moc-tools user configured on `mail.massopen.cloud`.  The public key from the keypair you just generated must be added to that user's `authorized_keys` file.

The moc-tools user on the mailman server must be a member of the `mailman` group to run the subscribe commands sent via the `addusers.py` script.

***

### Create an OpenStack service user

It would be possible for the scripts to use the admin user directly (and this is recommended in development environments) but in a production environment, we want to be able to disable the relevant user with minimal impact if this VM is compromised.  So create an openstack user with the  'moc-openstack-tools' and give it the `admin` role in the `services` project.

The credentials for this user go in the `[auth]` section of `settings.ini`:

    admin_user = moc-openstack-tools
    admin_project = services
    admin_password = user's_openstack_password_here
    auth_url = keystone_v3_endpoint goes here

Make sure to use the keystone v3 endpoint for auth_url.

***

### Set up the Google Forms and Google Sheets

The easiest way to do this is to create a copy of the existing form, then link it to a new spreadsheet.

Make sure to insert 3 new columns on the left and label them `Approved`, `Helpdesk Notified`, and `Reminder Sent`.  Delete all but 2-3 blank rows.

Duplicate the `Form Responses 1` sheet and rename the duplicate to `Current Users` (for the Access Request Form) and `Processed Requests` (for the Resource Request form).

If there isn't one already, set up a Google API Service Account key, as described in the README of the moc-openstack-tools repo.  There will be a fake email address associated with this key.  Share the new spreadsheets with your Google API key.  Uncheck the 'Notify people' box because since this isn't a real email address, the email will bounce back.  (But if you forget, it's not an issue, just ignore the bounced email.)

The key used for MOC production environments is checked into this private `moc` repo.  Never commit this key to the public `moc-openstack-tools` repo.

### Configure moc-openstack-tools

Fill out the settings.ini file.  This file is in .gitignore because it contains an admin password.  But, a current copy for each MOC helpdesk VM is maintained in the private `moc` repo.  Never commit these to the public moc-openstack-tools repo.

The comments in this file explain what each section does.  

***

### Set up the helpdesk interface

As root:

    # cp ~moc-tools/production/helpdesk/bash_profile.sh /etc/profile.d/helpdesk.sh
    # cp ~moc-tools/production/helpdesk/sudoers_file /etc/sudoers.d/helpdesk

Note, you can call the folder containing the repo anything you like - just make sure the paths in /etc/profile.d/helpdesk.sh and /etc/sudoers.d/helpdesk match.

If you are testing changes to /etc/profile.d/helpdesk.sh, you need to log out of the helpdesk user and log back in - the script gets loaded on login like .bashrc.

***
### Put the notifications script in cron

As moc-tools user, type `crontab -e`  (or as root, `sudo crontab -e -u moc-tools`)

Add this line:

     25 * * * * /bin/python /usr/local/src/moc-tools/production/check-approved-requests.py

This will run the script at 25 minutes past the hour, once per hour.  (You can change it to any time.)

***
### Set up the Postfix server

    $ sudo yum install postfix
    
Edit `/etc/postfix/main.cf` and add the following at the end of the file:

     # TLS Setup
     smtpd_tls_cert_file = /path/to/cert/file
     smtpd_tls_key_file = /path/to/key/file
     smtpd_tls_security_level = may

Replace `/path/to/cert/file` and `/path/to/key/file` with the paths of your server's SSL certificate and key files.  (In some cases this could be the same file.)

You may generate a cert with the following command (as root):

     # openssl req -new -x509 -nodes -out /etc/postfix/postfix.pem -keyout /etc/postfix/postfix.pem -days 3650

If you use this command, then the TLS Setup portion of your `/etc/postfix/main.cf` should look like this:

     # TLS Setup
     smtpd_tls_cert_file = /etc/postfix/postfix.pem
     smtpd_tls_key_file = /etc/postfix/postfix.pem
     smtpd_tls_security_level = may

Then, start postfix:

     $ sudo systemctl start postfix
     $ sudo systemctl enable postfix

You will need to restart postfix any time that you edit your main.cf configuration file, in order for those changes to take effect:

     $ sudo systemctl restart postfix 

*** 

### (If needed) Deploy Setpass

Setpass may be deployed in the Helpdesk VM, or separately.  In Kaizen, Setpass is deployed on the info.massopen.cloud website instead.  In Engage1, it is deployed on the helpdesk VM.

##### Install setpass

As centos user:

    $ cd
    $ git clone https://github.com/CCI-MOC/setpass
    $ cd setpass
    $ sudo cp etc/setpass.conf /etc/setpass.conf
    $ sudo pip install -r requirements.txt
    $ sudo python setup.py install
    
##### Set up the Apache server

As centos user: 

    $ sudo yum install httpd mod_wsgi mod_ssl -y
    $ cd ~/setpass
    $ sudo cp setpass/httpd/apache.conf /etc/httpd/conf.d/setpass.conf
    $ sudo mkdir /var/www/setpass
    $ sudo cp setpass/httpd/setpass.wsgi /var/www/setpass/
    $ sudo chown apache:apache /var/www/setpass -R

Make the following edits to `/etc/httpd/conf.d/setpass.conf`: 

1. By default setpass will listen on port 5001, but you can change this in this file by changing 5001 to a different port in the `Listen 5001 https` and `<VirtualHost *:5001>` lines.  Whatever port you choose, make sure your VM's security groups (or firewall, if it is not an OpenStack VM) are configured to open that port.

2. Replace `/PATH/TO/CERT.crt` and `/PATH/TO/KEY.key` with the correct paths for your server's SSL certificate and key file. 

Either turn off SELinux, or add the setpass port you configured in the /etc/httpd/conf.d/setpass.conf to the list of SELinux allowed ports, as described [here](https://stackoverflow.com/questions/17079670/httpd-server-not-started-13permission-denied-make-sock-could-not-bind-to-ad)

##### Set up the mariadb database

    $ sudo yum install mariadb mariadb-server mysql-devel
    $ sudo pip install MySQL-python
    $ sudo systemctl start mariadb
    $ sudo sysetmctl enable mariadb
    $ sudo mysql_secure_installation

Follow the prompts to set a root password and secure your mariadb installation.  Create a .my.cnf file for covenience:

    [client]
    user=root
    password=  <database root password you just set>

Type `mysql` to log into the mariadb prompt.

Alternatively, if you don't want to create a .my.cnf file, you can log in like this:

     $ mysql -u root -p
     Enter password:

You will now initialize the setpass database and its user.  Beforehand, generate some reasonably secure password for your setpass database user. `mysecret` to represents this password below.

     MariaDB [none]: Create database setpass;
     MariaDB [none]: use setpass;
     MariaDB [setpass]: create user 'setpass'@'localhost' identified by 'mysecret';
     MariaDB [setpass]: grant all on setpass.* to 'setpass'@'localhost';

Edit `/etc/setpass.conf` and change the `database` line to:

     database = "mysql:///setpass:mysecret@localhost/setpass"

Edit `~moc-tools/production/settings.ini` to fill in the `setpass_url` parameter:

     setpass_url = https://<public_ip_or_DNS_name_of_setpass_server>:5001

Note: don't use 'localhost' here even if Setpass is running on the helpdesk VM, because this value is also used to generate the link for end users to follow.

##### Start Setpass

     $ sudo systemctl start httpd
     $ sudo systemctl enable httpd

##### Updating Setpass

When new commits are merged to the Setpass master and you want to deploy them, do this (as the centos user):    

     $ cd ~/setpass
     $ git status     # check that you are on master
     $ git pull origin master
     $ sudo python setup.py install
     $ sudo systemctl restart httpd
