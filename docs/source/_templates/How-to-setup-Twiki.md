###How to Install Twiki in Ubuntu 14.04.02 LTS
####What is TWiki?
TWiki is a Perl-based structured wiki application typically used to run a collaboration platform, knowledge or document management system, a knowledge base, or team portal. Users can create wiki pages using the TWiki Markup Language, and developers can extend wiki application functionality with plugins. The TWiki project was founded by Peter Thoeny in 1998 as an open source wiki-based application platform. In October 2008, the company TWiki.net, created by Thoeny, assumed full control over the TWiki project while much of the developer community forked off to join the Foswiki project.
####Prerequisite:

#####Packages you need to install. 

`sudo apt-get install rcs`

#####Check perl version on your machine. 

`/usr/bin/perl -v`

#####It works well if you see the following words on your terminal. 

This is perl 5, version 18, subversion 2 (v5.18.2) built for x86_64-linux-gnu-thread-multi (with 41 registered patches, see perl -V for more detail) Copyright 1987-2013, Larry Wall 

####Installation:
#####Download the 6.0.1 version of Twiki: 

`wget "http://downloads.sourceforge.net/project/twiki/TWiki%20for%20all%20Platforms/TWiki-6.0.1/TWiki-6.0.1.tgzr=http%3A%2F%2Fsourceforge.net%2Fprojects%2Ftwiki%2Ffiles%2FTWiki%2520for%2520all%2520Platforms%2FTWiki-6.0.1%2F&ts=1419896584&use_mirror=superb-dca3" -O Twiki-6.0.1.tgz `

#####Unzip the package: 

`tar -xvf Twiki-6.0.1.tgz `

#####copy the entire Twiki package to the Apache directory in Ubuntu 

`sudo cp twiki/ /var/www/html/ -r `

#####Give the root access to the user:

`sudo chown www-data /var/www/html/twiki -R `
`sudo chgrp www-data /var/www/html/twiki -R `

#####Create the LocalLib.cfg. One simple way is to copy LocalLib.cfg.txt as LocalLib.cfg: 

`cd /var/www/html/twiki/bin `
`sudo cp LocalLib.cfg.txt LocalLib.cfg `
`sudo vim LocalLib.cfg `

#####If you put twiki under /var/www/html/twiki，you need to change: 

`$twikiLibPath = "/var/www/html/twiki/lib"; `

#####Set up Apache. http://twiki.org/cgi-bin/view/TWiki/ApacheConfigGenerator
![](https://github.com/xuhang57/MOC/blob/master/graphs/twiki/unnamed.png)

#####under the /var/www/html/twiki/bin , we need to do: 

`cd /var/www/html/twiki/bin` 
`sudo cp .htaccess.txt .htaccess `

#####Set up Apache:

`cd /etc/apache2/conf-available`
 
Vim a new file called twiki.conf: 

`sudo vim twiki.conf`
 
Copy everything you have done to this file and save

#####Link the twiki.conf file: 

`cd.. `
`cd conf-enabled `
`sudo ln -s ../conf-available/twiki.conf twiki.conf `

#####Restart the Apache 

`sudo a2enmod rewrite cgi`
 
#####Under /etc/apache2/ to find apache2.conf and rewrite: 

`<Directory /var/www>`
`Options Indexes FollowSymLinks`    
`AllowOverride All `   
`Require all granted ` 
`</Directory>`

`Restart the Apache` 
`sudo service apache2 restart` 

#####Go to your broswer and find: http://yourserveraddress/twiki/bin/configure :
![](https://github.com/xuhang57/MOC/blob/master/graphs/twiki/unnamed-1.png)

#####Reset the password
![](https://github.com/xuhang57/MOC/blob/master/graphs/twiki/unnamed-2.png)

######Reference:
http://chaoman.com/tech/servers/17-ubuntu-14-04-1-lts-