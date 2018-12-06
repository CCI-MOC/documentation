**NOTE:** If you will be using PuTTY on Windows, please read [this first](#ssh-keys-with-putty-on-windows).

#### Add a Key Pair
For security, the VM images have password authentication disabled by default, so you will need to use an SSH key pair to log in.

Click Access and Security, then click the Key Pairs tab which appears below it.

[[tutorial_screenshots/kilo/key_pairs_01.png]]

#### Import a Key Pair  
Prerequisite: You need ssh installed in your system

You can create a key pair on your local machine, then upload the public key to the cloud.  This is the recommended method.
   
Open a terminal and type the following commands (in the example, we have named the key cloud.key, but you can name it anything you want):
   
     $ cd ~/.ssh
     $ ssh-keygen -t rsa -f ~/.ssh/cloud.key -C "label_your_key" 

Example:

[[tutorial_screenshots/kilo/generate_key.png]]
 
You will be prompted to create a passphrase for the key.  
**IMPORTANT:** Do not forget the passphrase! If you do, you will be unable to use your key.

This process creates two files in your .ssh folder:

     cloud.key      # private key - don’t share this with anyone, and never upload it anywhere ever
     cloud.key.pub  # this is your public key    

**Pro Tip:** The `-C "label"` field is not required, but it is useful to quickly identify different public keys later.  You could use your email address as the label, or a user@host tag that identifies the computer the key is for.  For example, if Laura has both a laptop and a desktop computer that she will, she might use -C "Laura@laptop" to label the key she generates on the laptop, and -C "Laura@desktop" for the desktop.

On your terminal:  

     $ pbcopy < ~/.ssh/cloud.key.pub  #copies the contents of public key to clipboard 
    
Go back to the Openstack Dashboard, where you should still be on the Key Pairs tab
(If not, find it under Project -> Compute -> Access and Security --> Key Pairs)

Choose "Import Key Pair"

Paste the public key that you just copied in the "Public Key" text box.  
Give the key a name in the "Key Pair Name" Box.   

[[tutorial_screenshots/kilo/import_key.png]]

Click "Import Key Pair".  You will see your key pair appear in the list.

[[tutorial_screenshots/kilo/key_pairs_02.png]]  

You can now skip ahead to [Adding the key to an ssh-agent](#agent)
    
#### Create a Key Pair

If you are having trouble creating a key pair with the instructions above, the Openstack dashboard can make one for you.

Click "Create a Key Pair", and enter a name for the key pair.

[[tutorial_screenshots/kilo/create_key.png]]

You will be prompted to download a .pem file containing your private key.  (In the example, we have named the key 'cloud_key.pem', but you can name it anything.)  Save this file to your hard drive, for example in your Downloads folder.

[[tutorial_screenshots/kilo/save_key.png]]  

Store this key inside the .ssh folder on your local machine/laptop, using the following steps: 
    
     $ cd ~/Downloads          # Navigate to the folder where you saved the .pem file
     $ mv cloud.pem ~/.ssh/    # This command will copy the key you downloaded to your .ssh folder.  
     $ cd ~/.ssh               # Navigate to your .ssh folder
     $ chmod 400 cloud.pem     # Change the permissions of the file

To see your public key, navigate to 
Project -> Compute -> Access and Security -> Key Pairs

You should see your key in the list.

[[tutorial_screenshots/kilo/key_pairs_03.png]]

Click on the key, and you will see a screen of information that includes your public key:

[[tutorial_screenshots/kilo/view_public_key.png]]

The public key is the part of the key you distribute to VMs and remote servers.  You may find it convenient to paste it into a file inside your .ssh folder, so you don't always need to log into the website to see it.  Call the file something like `cloud_key.pub` to distinguish it from your private key.

**Important: Never share your private key with anyone, or upload it to a server!**

#### <a name="agent"></a>Adding the key to an ssh-agent

If you have many VMs, you will most likely be using one or two VMs with public IPs as a gateway to others which are not reachable from the internet.  In order to be able to use your key for multiple SSH hops, do NOT copy your private key to the gateway VM!  The correct method to use Agent Forwarding, which adds the key to an ssh-agent on your local machine and 'forwards' it over the SSH connection.

First, add the key to your ssh agent:

     $ cd ~/.ssh
     $ ssh-add cloud.pem

Check that it is added with the command

     $ ssh-add -l
     2048 SHA256:D0DLuODzs15j2OaZnA8I52aEeY3exRT2PCsUyAXgI24 /Users/kamfonik/.ssh/cloud.pem (RSA)

Depending on your system, you might have to repeat these steps after you reboot or log out of your computer.  You can always check if your ssh key is added by running the `ssh-add -l` command.  A key with the default name id_rsa will be added by default at login, although you will still need to unlock it with your passphrase the first time you use it.

Once the key is added, you will be able to forward it over an SSH connection, like this:

     $ ssh -A -i cloud.key <username>@<remote-host-IP>

Connecting via SSH is discussed in more detail later in the tutorial ([[SSH to Cloud VM]]); for now, just proceed to the next step below.

#### SSH keys with PuTTY on Windows

PuTTY requires SSH keys to be in its own `ppk` format.  To convert between OpenSSH keys used by OpenStack and PuTTY's format, you need a utility called PuTTYgen.  If it was not installed when you originally installed PuTTY, you can get it here: [Download PuTTY](#http://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

You have 2 options for generating keys that will work with PuTTY:

1. Generate an OpenSSH key with ssh-keygen or from the Horizon GUI using the instructions above, then use PuTTYgen to convert the private key to .ppk

or

2. Generate a .ppk key with PuTTYgen, and import the provided OpenSSH public key to OpenStack using the 'Import' instructions above.

There is a detailed walkthrough of how to use PuTTYgen here: [Use SSH Keys with PuTTY on Windows](#https://devops.profitbricks.com/tutorials/use-ssh-keys-with-putty-on-windows/)

***

#### Next:  [[Launch a VM]]  
###### Previous:  [[Security Groups]]  
[[Openstack Tutorial Index]] 

<!--
#### Adding ssh key to ssh-agent

The following commands will be executed on your local machine/laptop preferably having 
Linux flavor.
On your terminal start ssh-agent:     
$ eval "$(ssh-agent  -s)"  
Agent pid 5966  
$ ssh-add ~/.ssh/"new-key"  #the private key  

## Allocate Floating IP's to Project
Floating IPs allow virtual machine instances to be accessed from outside of the openstack network. Floating IPs can be allocated to a project before or after launching an instance.

Your project is assigned one floating IP. You need to allocate the IP to your Project.

Goto Project -> Access and Security -> Floating IPs tab
 
<img src=http://i.imgur.com/VvaAa0l.png>
  
Click on "Allocate IP to Project" button.  

<img src=http://i.imgur.com/yapQW8B.png>  

Click on Allocate IP below the Project Quotas. 

You can see the new Floating IP assigned to your Project under Floating IP's tab.

<img src=http://i.imgur.com/oRs9O7o.png>   
-->
