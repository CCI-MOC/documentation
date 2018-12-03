# MOC Website

### Wordpress
* [Setup LAMP](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-centos-7)
* [Create DB user](https://www.digitalocean.com/community/tutorials/how-to-create-a-new-user-and-grant-permissions-in-mysql)
* [Install Wordpress](https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-on-centos-7)
* In `/etc/httpd/conf/httpd.conf` under `<Directory "/var/www">` and `<Directory "/var/www/html">` change `AllowOverride none` to `AllowOverride all`.  

*IMPORTANT** Do not change it under `<Directory "/">`!*

```
Admin User moc
Admin Pass <admin password, see Wordpress Admin>

mysql_root_password: <root password set during secure installation>
wp_db_name: wordpress
wp_db_user: wpuser
wp_db_pass: <password set for wpuser>
```
### How to change Wordpress Domain 
When running a multisite installation as we are, wordpress also hardcodes the domain in the database.

Run this [script](https://github.com/interconnectit/Search-Replace-DB) through the command line to replace all occurrences of the old domain with the new domain. (Can't be done manually as there are hundreds). [Reference](https://codex.wordpress.org/Moving_WordPress#Moving_WordPress_Multisite)

Navigate to the wordpress directory (this is where `wp-config.php` is) and run:
    
     # git clone https://github.com/interconnectit/Search-Replace-DB
     # cd Search-Replace-DB
     # php srdb.cli.php -h localhost -n <wp_db_name> -u <wp_db_user> -p <wp_db_pass> -s "<old_domain>" -r "<new_domain>" -x "guid"

Replace all the `< >` values with the mariadb credentials, old domain, new domain, etc.

Next, change all occurrences of the old domain in `wp-config.php`. 

You must also replace the domain name everywhere it is hard-coded into links in the html directory:

     # grep -rl "<old_domain>" /var/www/html | xargs sed -i 's|<old_domain>|<new_domain>|g'

There is also a GUI you can use by pointing your browser to the Search-Replace-DB directory.

If the script gives you a CLI error about `undefined function mb_regex_encoding()` it is because you need to install the package php-mbstring.  (In the GUI this will appear as a popup about an AJAX error).  

Install the package, then restart httpd
     
     # yum install php-mbstring -y
     # systemctl restart httpd

### How to Restore from Backup
The backup consists of:

    /var/www/html      # entire web directory
    server_backup.sh   # the customized website backup script
    mysql-web1.sql     # backup of the wordpress database
    httpd.conf         # server config
    backup.info        # identifying info about the backup, time/date, etc

Use Kristi's Ansible playbook to automatically deploy a LAMP server and configure the wordpress database on a new instance.

*(Update on Spet. 15th by Lucas: Currently, the ansible script might not work. We need to do a test on that script)*

Copy the backup of `/var/www/html` to `/var/www/html` on the new server.  You need to make sure all hidden files are copied, so be careful of the syntax:

     # rmdir /var/www/html
     # cp -r <backup_directory>/var/www/html /var/www/html
     # cp httpd.conf /etc/httpd/conf/httpd.conf
     # systemctl restart httpd

*Note: If you are doing a test with a different IP, or restoring to a new domain name that isn't 'massopen.cloud', you will also need to follow the migration instructions above to update the domain name in the wordpress database.*

### Joomla 
```
Joomla DB user/password
Joomla Admin user/password 

```

### Twiki
* Twiki config password `Welcome1` 
* Needed to install perl dependencies as instructed [here](http://twiki.org/cgi-bin/view/TWiki/HowToInstallCpanModules)

### Google Forms
Email Notification for Forms

There are two versions of google form now. Depends on your personal setting, you will see different versions.

**Version1**

In the top left nav. menu, you will see an "Add-ons" menu and after clicking on it, you will see an "Email Notification for Forms" option that you can click. And just click manage notifications.

**Version2** 

Exactly the same except for the nav. menu is on the top right. 

Currently, we have Kaizen Resource Request Form using version 2 and MOC OpenStack User Request Form using version 1.

