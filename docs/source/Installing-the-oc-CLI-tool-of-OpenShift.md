oc command line tool is used to access the Openshift cluster through the command line. This gives more flexibility as compared to the web interface.

### Downloading

### Linux
For Linux 64 bits, download the following link : 
[Linux 64-bit OpenShift oc download](https://github.com/openshift/origin/releases/download/v1.5.1/openshift-origin-client-tools-v1.5.1-7b451fc-linux-64bit.tar.gz)

`wget https://github.com/openshift/origin/releases/download/v1.5.1/openshift-origin-client-tools-v1.5.1-7b451fc-linux-64bit.tar.gz`

### MAC
[MAC OpenShift oc download](https://github.com/openshift/origin/releases/download/v1.5.1/openshift-origin-client-tools-v1.5.1-7b451fc-mac.zip)


### Windows
[Windows OpenShift oc Download](https://github.com/openshift/origin/releases/download/v1.5.1/openshift-origin-client-tools-v1.5.1-7b451fc-windows.zip)

Now, extract the downloaded file.
In linux use tar

`tar –xvf openshift-origin-client-tools-v1.5.1-7b451fc-linux-64bit.tar.gz`

P.S: to find the latest version, go to [oc releases](https://github.com/openshift/origin/releases) and scroll down to the downloads section.

Now, you might want to change the name of the file to something small. Use the mv command for the same.

`mv openshift-origin-client-tools-v1.5.1-7b451fc-linux-64bit oc-tool`

If you do ls, you should find the folder oc-tool. Go into the folder using cd

`cd oc-tool`

In this folder, you will find the oc command which will be used for all operarions.

To avoid typing ./oc every time, store this path in your .bashrc file.

`export PATH=<HOME>/oc-tool:$PATH`

Now to login, do

`oc login`

You will be asked for the url of your openshift cluster.
Enter the complete url with the port number and hit enter.

`ubuntu@vm:~/oc-tool$ oc login`

`Server [https://localhost:8443]: https://128.XX.XX.XX:8443`

Now enter your username and password.

After successful, login you will be able to see your projects in your account. To switch between projects use

`oc project <project-name>`  

 


 