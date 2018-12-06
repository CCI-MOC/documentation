<!--## SetUp Proxy Server on your browser
### Firefox 

First, pick a local port number to act as your socks proxy port. We'll call it `$SOCKS_PORT_NUMBER`

You can refer to this online link [setup proxy on firefox](http://www.wikihow.com/Enter-Proxy-Settings-in-Firefox) or go thru the following instructions:  
- Go to Firefox -> Preferences -> Advanced -> Network  
- Click on Settings -> Enable Manual Proxy Config  
- SOCKS HOST: “localhost” Port $SOCKS_PORT_NUMBER  //example 5000 (ensure port number is greater than 1024)

Check this screenshot.

<img src=http://i.imgur.com/HTd6jPfh.png> 

### Google Chrome (Ubuntu)    

We'll call a local port number as the socks proxy port. We'll call that port number as `$SOCKS_PORT_NUMBER`  

- Open a Google Chrome browser. Maneuver to the top right to Customize and Control Google Chrome (Preferences).  
- Underneath the dropdown, select Settings. A new tab with the Settings will open.  
- Go to the bottom of Settings and click on Show advanced settings...  
- Find the header named Network and select Change proxy settings... A window with Network settings pops up.
- Select Network tab, which is on the left side. Select Manual method. 
- Next to Socks Host, input: localhost 
- Input the port number before the - and + buttons: $SOCKS_PORT_NUMBER //example 5000 (ensure port number is greater than 1024)  
- Finally, hit the button with Apply system wide.

<img src=http://i.imgur.com/7XALCtM.png> 

Before visiting the OpenStack Dashboard, you may have to restart the browser.  

## SSH Port Forwarding to get access to the SSH-Gateway
    ssh -D $SOCKS_PORT_NUMBER $BU_USERNAME@140.247.152.200 -N  #the ssh user name would be one from Checklist point 2.
**Sample Example:**  
   ssh -D 5000 abhi@140.247.152.200 -N 

The first time you ssh in, you should receive this message (the keys should match):

    The authenticity of host '140.247.152.200 (140.247.152.200)' can't be established.
    ECDSA key fingerprint is 58:5d:2f:a4:44:d6:71:0c:5a:85:ac:96:14:1c:e4:71.
-->
## Navigate to the the OpenStack Dashboard  
<!--Point your browser(with proxy server enabled) to http://140.247.152.207-->
Point your browser to https://kaizen.massopen.cloud

You should see the following screen:      

[[tutorial_screenshots/kilo/redhat_login_page_small.png|Red Hat Openstack Login Page]]

<!--img src=http://ataturk.github.io/tutorial/login.png-->
<!--img src=http://i.imgur.com/6MsO98m.png-->

<!--If you can see OpenStack dashboard Awesome !-->

## Log in to the OpenStack Dashboard
Use your openstack user credentials to login.  Your username will be your email address.  Upon signing up you received a temporary password.  Use the temporary password the first time you log in, then immediately change it (otherwise it will expire and you will not be able to log in).

Once you login you should see an overview of the resources like instances(vms), CPU, RAM etc. Take some time to
just browse around.

[[tutorial_screenshots/liberty/compute_overview.png|Project Overview]]
<!--img src=http://i.imgur.com/ZTK0J5i.png-->  

## Change Your Password
The first time you log in, you must change your temporary password to one that only you know.  Find your username in the top right corner of the dashboard, and click on it.  Choose "Change Password" from the menu that opens.  Enter your temporary password under "Current Password", and your new password in the other two boxes.

[[tutorial_screenshots/kilo/change_password.png|Change Password Menu]]

***
 
#### Next:  [[Dashboard Overview]]  
[[Openstack Tutorial Index]]   
