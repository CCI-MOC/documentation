# Openshift Setup

### Prerequisites:
In order to access openshift, we first need to login to the openshift machine in Engage1 Server.

	ssh -A -D <port no.> khareak@128.52.62.147 -p 2244

*NOTE:You need to have the key set up in this system in order to use it.*

This opens up a port which we need to use to set up a manual socks proxy in our Internet browser.

Enter the url `https://openshift.eng1.moc.edu:8443/console`

In order for the url to work successfully, add the IP and the hostname to the machine from which you are going to access to `/etc/hosts`

For eg:
	
	10.13.66.11 openshift.eng1.moc.edu

Login with the user id you created, and start creating projects!!!

Enter your user id and password and you are open to use openshift.

*NOTE: For setting up a new user name in openshift follow the [readme](https://github.com/CCI-MOC/openshift/blob/master/README.md)*

You can login to the CLI with the commands:

	oc login

You can create a new project using the UI or the CLI:
* For UI, you just need to click on `Create New Projects`
* For CLI, type in `oc new project "project-name"`

### Jenkins Project configuration
By default, a jenkins image is provided by Redhat, and it comes in both Rhel and centOs.

First the image needs to be pulled, so that openshift can recognize the image to create an app.

**For Redhat** :

	docker pull registry.access.redhat.com/openshift3/jenkins-1-rhel7

**For CentOs** :

	docker pull openshift/jenkins-1-centos7

*NOTE: It is recommended that we use **CentOs image**, since Redhat image has a bug which does not allow it to deploy pods successfully.*

Now that we have the image ready, we need to create an app based on this image.

	oc new-app  -e JENKINS_PASSWORD=_password_ openshift/jenkins-1-centos7

This will deploy the application.

In order to visit the app, there are two ways to go about it.

	oc expose service/_name_ --hostname=www.example.com    

This enables the host to present the jenkisn ui in the hostname `www.example.com`.

Unfortunately this option is not feasible in engage1 server since we do not have a public ip associated to the master ip.

The second way is to get the private ip of the service (this is possible by clicking on the service in the ui), and then enter the private ip in the browser as `http://privateip:8080`.

Now this will only work if you have a proxy set up. 

In your browser, go to your proxy settings and set up a manual socks proxy with any port (say 6000).

Then SSH into your machine where openshift is currently running using the port 6000 with the below command:

	ssh -D 6000 username@ipaddress

Jenkins is now ready to be used.

### Using Jenkins' modified image - S2I
Inorder to use S2I, first you need to install the S2I tool, which you can do from [here](https://github.com/openshift/source-to-image/releases/tag/v1.1.0).

[More information on I2I](https://github.com/openshift/source-to-image)

Once you have made necessary configurations in jenkins for you projects, you can create an image for that in openshift and then use this new image instead of the default image given.

[some documentation on using s2i](https://docs.openshift.com/enterprise/3.2/using_images/other_images/jenkins.html#jenkins-as-s2i-builder)

[some documentation on using Jenkins with Openshift](https://github.com/openshift/jenkins)

Even though it looks straight forward, I'll explain the steps for using s2i.
1. First of all **create a private git repository** since your passwords will all be visible in your jenkins' configuration.
2. **Create the directory structure** as mentioned in the above links.
3. **Copy all the files** from `/var/lib/jenkins` within the jenkins container which is running in Openshift into the configurations folder in git repository.
4. **Copy all the files and folders** from `/var/lib/jenkins/plugins` into the plugins folder in git repository.
5. **Copy all the files and folders** from `/var/lib/jenkins/jobs` into the configurations/jobs folder in git repository.
6. **Mention all the correct plugins and their versions in the `plugins.txt`** file inorder to successfully install the extra  plugins which were not present already in the default jenkins project.
7. **Push files into the github private repo**.
8. Now goto the directory where s2i is installed and **run command**

	./s2i build https://github.com/_repository-name_ _existing-image-to-be-based-on_ _new-image-name_

A new image should be ready, and can be used after it is tagged and pushed to the docker registry.

### Creating a Slave pod for jenkins 
The ideal way to use jenkins is to have a master, and then everytime there is a pull request, run the test cases on a new slave pod and delete the pod once the test cases have run successfully.

This allows to use the environment of different machines to be utilized by the slaves using a single master.

Complete details are given [here](https://blog.openshift.com/openshift-3-2-jenkins-s2i-slave-pods/).

In the above mentioned blog, there are two lines about Kubernetes plugin, and that _has_ to be configured on order to make this work efficiently.
* Kubernetes url can be found using the below command, with port 443 mostly
```
oc get services --all-namespaces
```
* Kubernetes Server certificate key would be the first server key found using this command
```
kubectl config view --raw
```
* Kubernetes namespace would be the project name you are currently under.
* Credentials would be te username/password currently being used in openshift.
* Make sure to test the connection before proceeding forward.
* Jenkins URL would be the `https://private_ip:8080`
* The pod template should match with the name you are going to set your slave image as per the blog.
* The label should match with the label you give in your project configuration.

These configuration should enable jenkins to run a slave pod successfully.

