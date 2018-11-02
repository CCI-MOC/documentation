### General info and FAQ on using mail and lists @ massopen.cloud

* To view lists and subscribe/unsubscribe visit https://mail.massopen.cloud/mailman/listinfo and read the instructions provided by Mailman.

### I lost my email communication from mailman, how can I retrieve my password ?

Follow instruction on [this html page](http://www.list.org/mailman-member/node16.html).
On the html page, there is a mentioning of **http://WEBSERVER/mailman/listinfo/LISTNAME**
For the MOC : 

WEBSERVER is **mail.massopen.cloud**

LISTNAME is the name of a MOC mailman email list. 

For example, if you want to retrieve your password credential for the "bmi" mailing list, the link you want to go to is **http://mail.massopen.cloud/mailman/listinfo/bmi** 

Full MOC mailing list is [here](https://mail.massopen.cloud/mailman/listinfo). 

### How does email box on massopen.cloud work ?

Once your account is created visit https://mail.massopen.cloud/roundcubemail/  login with your initial password. **Username is only username, not email address**. Then visit Settings -> Identity to set display name ( this applies only when using the roundcube web client ),  Settings -> Password to change password.

Mailbox can also be accessed using any desktop email client. Inbound and outgoing server is the same - mail.massopen.cloud. Inbound protocol is IMAP over SSL, port 993. Outgoing protocol SMTP over SSL, port 465. Microsoft Outlook can also use active sync type of mail server - settings are detected automatically (if not - mail server is mail.massopen.cloud, **username only**), and display name will be set automatically

For mobile devices use **Exchange/Active sync** type of email. In that case only information needed is the email address and password. All settings are configured automatically - tested on iphones.
