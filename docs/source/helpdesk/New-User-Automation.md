<!-- define outside link text here --> 
[Google Spreadsheet]: https://docs.google.com/spreadsheets/d/1lWjxBp79Taog-DiF7hU0HEZEHGk1n5we8oDr_xzO8IQ
[Google Form]: https://docs.google.com/forms/d/1-Kkx9qbsNT4-gWd8_VUVEPO420Or6L8RhpVTyke5svI
[addusers.py]: https://github.com/CCI-MOC/moc/tree/master/scripts/addusers
[Google Developer's Console]: https://console.developers.google.com/
[signup form]: http://massopen.cloud/blog/user-account-request-form/
[setpass github]: https://github.com/CCI-MOC/setpass
[moc-openstack-tools]: https://github.com/CCI-MOC/moc-openstack-tools
<!-- define internal link text here --> 
[spreadsheet]: #google-spreadsheet
[addusers script]: #addusers-python-script
[form]: #google-form
[wordpress plugin]: #wordpress-plugin
[setpass]: #setpass-service
[password-reset]: #reset-a-password
[helpdesk notification script]: #helpdesk-notification-script
[UMass helpdesk]: #umass-helpdesk
## Overall Flow 
There are many interconnected steps in the process of adding a new user.  The flow of information looks like this:

1. user fills out the [form]  
1. [form] writes the responses to the [spreadsheet]
1. [wordpress plugin] sends an email notification to Piyanai
1. Piyanai marks the request approved (or moves it to Spam)
1. The [helpdesk notification script] checks the spreadsheet periodically, and when it finds a new approved request, it sends an email to the [UMass helpdesk] to generate a ticket
1. The helpdesk runs the [addusers script] to process the request
1. [addusers script] uses the [service account](#google-api-project-and-service-account) to get access to the [spreadsheet]
1. [addusers script] reads data from the [spreadsheet] and creates new users/projects
1. [addusers script] moves the processed data to the Current Users tab of the [Google Spreadsheet]
1. [addusers script] adds the user, password, and PIN to the [setpass] service
1. [addusers script] emails the users with a wecome email and a link to set their password.
1. There is also functionality for [resetting][password-reset] a user's password if they forgot it.

A process for making [backups of the Current Users tab](#backups-of-current-users) is coming soon.

Click on a link above to go to a deeper explanation of that component, or just keep reading below.

## Google Form

Users must sign up via a [signup form] on the MOC Website.  

The [Google Form] is owned by the `mocwebsiteacct` Gmail account.  Currently Piyanai, Lucas, and Laura have access.

The form does very little data validation other than requiring certain fields, but it does enforce a 4-digit, all-numbers PIN. It stores all responses in a [Google Spreadsheet] called `NewKaizenRegistrationForm (Responses)`.  

If there is some need to change the spreadsheet where responses are stored you can do that from the form editing page. You can also unlink an old spreadsheet if responses should not be saved there any longer.

In the newest version of Google Forms, it is possible to enable email notifications when a response is received.  This is currently disabled since email notifications are handled by the [wordpress plugin].  Note that the Google Forms emails always go to the form owner, there is no way to designate a specific email address. (You could achieve that by filtering and forwarding in the gmail account if needed).

## Wordpress Plugin

1) How to configure email notification
* Sign in to the Wordpress Dashboard
* Click "Google Forms" button on the left sidebar after you see the dashboard
* Choose the form you want to edit and click on it.
* Scroll down and put your email in the field of "Send to" 

2) How the form is connected to the website (and where to change this, if for example we needed to point to a new form)
* Same as above. Put your Google form URL in the "Form URL"


## Google Spreadsheet

This [Google Spreasheet] stores form responses and is owned by the `mocwebsiteacct` Gmail account. 

The responses are placed on a tab called `Form Responses 1`.  There is also a tab called `Current Users`, where user form responses are stored permanently once it is processed by the [addusers script].

Do not change the name of the spreadsheet or any tabs.  This is very likely to break interaction with both the form and the [addusers script].  If for some reason you need to rename things, be sure to make the appropriate updates to the form settings, addusers.py, and this documentation.


## Google API Project and Service Account 

The Google API project `project-mocwebsite` is owned by the mocwebsiteacct Gmail account and can be managed by logging into the [Google Developer's Console].

This API project has a Service Account named `mocwebsite`.  To manage it, click the menu near the top left next to 'Google APIs', and choose 'IAM & Admin'.  Then in the left sidebar, choose 'Service Accounts'.  

This page shows the service account ID: `mocwebsite@project-mocwebsite.iam.gserviceaccount.com`.  To grant the service account access to a resource (such as a spreadsheet):
  1. open the resource and click Share
  * choose Advanced
  * enter the resource ID in the 'Invite People' box 
  * from the dropdown, choose Edit permissions
  * uncheck `Notify people` (because the ID isn't really an email address, so the notification bounces)

This page also manages keys associated with the account.  You can create new keys or delete old ones from this page.  If you delete a key you will have to provide a new key file for any scripts/applications that were using the deleted key, such as the [addusers script].  

If you lose all copies of a key you can't re-download it, you must create a new key on this page and and put the new key file in place of the old one every place it is used.

## Helpdesk notification script

This script is called `check-approved-requests.py` and lives in the [moc-openstack-tools] repo.

It runs as a cronjob, most conveniently in the VM that is already set up to run the addusers script, since the Google API key and the mail server will already be there.

Each time it runs, it checks through the entries on both the access request spreadsheet and the quota request spreadsheet.  If it detects that an entry has been marked as Approved, but the Helpdesk Notified column is blank, then it will generate an email to the helpdesk which automatically creates a ticket.  Then it will enter a timestamp in the Helpdesk Notified column.  

## UMass Helpdesk

The UMass Helpdesk will see the Access Request and Change Quota Request tickets generated by the script.  They will log into the helpdesk VM:

    $ ssh helpdesk@128.31.25.252

And use the `moc` interface to run the script.  This is a bash function layered in between the helpdesk user and the actual scripts - the intention is to prevent all the helpdesk users from having access to the `settings.ini` file which contains passwords.  More information about this is at [[Configuration of the Helpdesk VM]].

## Addusers python script

The addusers script is stored in the private 'moc' repo at [this link][addusers.py].

### Initial setup
You will need an account on the addusers VM, contact Laura (kamfonik at bu.edu ) to get one.  Include your public key.  Once you have an account, you can log in at:  

    $ ssh <yourusername>@128.31.24.185 -p 2222

To be able to use the script, you must clone the repo.  Make a copy of the the `template_settings.ini` file and name it `settings.ini`. 

The credentials for reading and writing to the Google Sheet are specified in the `[excelsheet]` section of settings.ini:

    [excelsheet]
    auth_file = project-mocwebsite-5b5ccc23e55c.json
    worksheet_key = <worksheet key goes here>

Place a copy of the service account key, `project-mocwebsite-5b5ccc23e55c.json`, in the same directory as addusers.py.  See Laura or Shashank to get a copy of this key file.  Never check this file into the git repo.

The `worksheet_key` parameter can be found in the URL of the spreadsheet.  For example, if the spreadsheet URL might look something like this when you navigate to the spreadsheet:    
`https://docs.google.com/spreadsheets/d/1lWjxBp79Taog-DiF7hU0HEZEHGk1n5we8oDr_xzO8IQ/edit#gid=910075903`

The worksheet ID is the URL segment which follows spreadsheets/d/, so in this example it is:    
`1lWjxBp79Taog-DiF7hU0HEZEHGk1n5we8oDr_xzO8IQ`

Fill in the other missing parameters in settings.ini according to the instructions in the file comments.  This only needs to be done once, although the file does need to be kept up to date if anything changes.  For example, if a new spreadsheet replaces the old one, you would update the worksheet ID.

You should **NEVER** commit `settings.ini` into a git repo (not even a private one) or store it in any insecure place, since it contains an admin password for the OpenStack cluster.  The addusers directory already has a .gitignore configured to prevent it being committed accidentally.  

### Running the script

#####1. Double check the Google Spreadsheet
* If Project name is blank, find out what the project name should be and type it manually.  
* If the user is joining an existing project, make sure the project name is correct and spelled right.  For example people might type 'Mix & Match' but the correct OpenStack project name  is 'k2k'.
* For a new personal project, the project name will be the first portion of the email address. 
    * Example 1: user `kamfonik@myuniversity.edu` would get the project name `kamfonik` 
    * Example 2: user `l.k.kamf913@mycompany.com` would get the project name `l.k.kamf913`.
* Make sure all fields have something in them.  If Comment or Phone is blank, just type `None` there.  If any other information is blank, ask Piyanai before proceeding, since we want that information for all users.
* Double check that all the entries seem legit - if someone filled out form, Piyanai approved them, and then someone else filled out the form before you checked it, that entry could be bogus.

#####2. Log into the addusers VM and run the script.  Make sure to use the latest code:

    $ ssh <yourusername>@128.31.24.185 -p 2222
    $ cd moc/scripts/addusers           # assuming you cloned the repo in your home directory
    $ git status                        # make sure you are on the master branch
    $ git pull 	                        # get the latest code
    $ sudo python addusers.py

#####3. Check script output for errors
The script performs the following tasks:

1. Read and parse new user data from the spreadsheet
2. Create new OpenStack projects and users based on the data
3. Add all new users, passwords, and PINs to the Setpass service database
4. Copy all successfully processed rows to the `Current Users` tab
5. Delete all successfully processed rows from the `Form Responses 1` tab
6. Print any errors and warnings about rows it was unable to process

Any rows the script cannot process (including users that already exist) will not be copied to `Current Users` or deleted from `Form Responses 1`.  So if there is an error, you can take steps to address it and then run the script again if needed.

The script will not process any row that has blank fields, so make sure that for the users you want to add you have filled any blank fields.  It's fine to just type 'none' or 'N/A' or similar into the Phone and Comment fields if they left those blank.

The copying/delete steps are batched and happen after user creation is finished for all users.  So if the script ever creates a few users but then throws some exception and exits before the spreadsheet is updated, you will need to move those users to Current Users manually.

## Setpass Service
[Setpass][setpass github] is a tool originally created by Kristi which allows users to set their own passwords securely. 

It receives the user's randomized password, PIN, and user ID from the addusers.py script and stores them in a database along with a token.  The token is used in a URL given to the user.  The user goes to the URL and enters the same PIN that they used when signing up, along with the password they wish to use.  They have 3 attempts to get the PIN right.

Currently this service is running on port 5001 at massopen.cloud, so the URLs the user should are constructed like this: `https://massopen.cloud:5001?token=<token>`.

Setpass config info (goes in /etc/setpass.conf)

     database = "mysql://setpass:saddle-spotted-noncapital-hellhound@localhost/setpass"
     auth_url = 'https://keystone.kaizen.massopen.cloud:5000/v3'
     admin_project_name = "admin"
     admin_project_domain_id = "default"
     token_expiration = 86400
     max_attempts = 3

Apache config for setpass (goes in /etc/httpd/conf.d/setpass.conf):   

	LoadModule wsgi_module modules/mod_wsgi.so
	LoadModule ssl_module modules/mod_ssl.so

	Listen 5001 https

	<VirtualHost *:5001>
	  SSLEngine On
	  SSLCertificateFile /etc/httpd/ssl/server.crt
	  SSLCertificateKeyFile /etc/httpd/ssl/server.key

	  WSGIPassAuthorization On
	  WSGIChunkedRequest On
	  WSGIDaemonProcess webtool user=apache group=apache threads=5 home=/var/www/setpass/
	  WSGIScriptAlias / /var/www/setpass/setpass.wsgi

	  <Directory /var/www/setpass/>
	    Require all granted
	  </Directory>
	</VirtualHost>


## Reset a password
The addusers directory in the moc repo also contains a script called reset-password.py.  You can use this to generate a new link for a user like this:

    $ python reset-password.py <username> <pin>

If the user signed up recently you can probably use the same PIN they used at signup, which can be found in the Google Spreadsheet.  If it was a long time ago, depending on the situation you can either ask them to provide you with a new PIN, or just pick one yourself and provide it to them securely.

You can also use this to help recover from an error in the addusers script - for example, if a user was created but the script exited before adding them to setpass, just use this script to add them to setpass manually.

## Backups of Current Users

**Coming Soon!**
