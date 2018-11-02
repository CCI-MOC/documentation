Very few people need access to this switch; if you think you are one of them, talk to the ops team about getting an account on the MIT gateway server. 

### Accessing the switch GUI

##### Create VNC server
      $ ssh <username>@eofe1.mit.edu

If you've logged into the switch before, check to see if the VNC server you used last time is still there (they are generally persistent):

      $ ps –ef | grep vnc

The process will look something like this: 

      00:05:32 /usr/bin/Xvnc :3 -desktop eofe1:3 (kamfonik)

Here eofe1:3 corresponds to port 5903.

If there is no Xvnc process running, either because it died or because this is the first time you are logging in, just type 'vncserver' to launch one.  You will be prompted to create a password.  The output will tell you the port.

##### Log into the VNC server

Create a tunnel from your local machine:

      $ ssh -L 5903:localhost:5903 -A <username>@eofe1.mit.edu

(For the second port, make sure to use whatever port actually corresponds to your VNC server, which you noted in the previous step).

You should now have a local connection to the VNC server.  The exact method depends on your OS.  On Mac OSX, I do the tunneling in Terminal and then use Finder to connect.  Some VNC clients will do both steps for you.  You will need to log in with the password you created in the 'vncserver' step.

##### Log into the switch
Once you are on the VNC server, the switch is at R5-PA-C01-U39.ipmi.cluster in your browser. 

     Username: admin  
     Password: brocade123  

Note:  The first time you open the VNC Server GUI, turn off the screensaver!  Otherwise, you will be locked out when the screensaver activates, because it demands a password (that we don't have) to log back in.  If you forget and this happens, you will have to go and kill the screensaver process from eofe1 on the command line.

You can also SSH to the switch with the same credentials if you prefer to use the CLI.

##### Resetting the VNC server password

Even if the VNC server is stopped and relaunched, it will use the same password you set up the first time you launched it.  If you need to reset the password, kill the server's Xvnc process (if running) and then remove the file `.vnc/passwd` from your home directory.  Then run the `vncserver` command again.  You should be prompted to create a password.

### Accessing the switch CLI

It is also possible to log into the switch CLI. From eofe1, ssh directly to the IP of the switch.  Username and password are the same as above.



### Port Map

Ports not in the map are unused.

Port | Connection | Access/Trunk | VLAN(s) | Notes
--- | --- | --- | --- | ---
gi 1/0/2 | RBridge-6 console | access | 3040 |
gi 1/0/3 | ? | access | 3040 | configured but down
gi 1/0/4 | cache-c07-01 | trunk | 4004,1601 | I have this configured as a trunk for some testing; put it back to access on 4004 when done
gi 1/0/5 | cache ? | access | 4004 |
gi 1/0/6 | cache ? | access | 4004 |
gi 1/0/7 | cache ? | access | 4004 |
gi 1/0/8 | cache ? | access | 4004 |
gi 1/0/9 | uplink to MRI provisioning/IPMI switch | trunk | 1601, 1602 |
gi 1/0/10 | not connected | Testing | 1511-1520 | Naved is running tests for HIL
gi 1/0/11 | not connected | Testing | 1511-1520 | Naved is running tests for HIL
gi 1/0/12 | not connected | Testing | 1511-1520 | Naved is running tests for HIL
gi 1/0/13 | not connected | Testing | 1511-1520 | Naved is running tests for HIL
... | - | - | - |
gi 1/0/22 | cache ? | access | 3040 |
gi 1/0/23 | cache ? | access | 3040 |
gi 1/0/24 | cache ? | access | 3040 |
gi 1/0/25 | cache ? | access | 3040 |
gi 1/0/26 | cache ? | access | 3040 |
.. | - | - |
gi 1/0/48 | connection to moc-jun3300 | trunk | 3040, 4004, 1601, 1602 |

