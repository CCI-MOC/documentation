<!-- linky links -->
[CLI Cheat Sheet]: http://docs.openstack.org/user-guide/cli-cheat-sheet.html
[Openstack End User Guide]: http://docs.openstack.org/user-guide/
[1]: http://docs.openstack.org/user-guide/common/cli-install-openstack-command-line-clients.html

## Background

Most of this tutorial has been focused on interacting with OpenStack via the Horizon GUI.  But anything you can do in Horizon can also be done by interacting directly with the Openstack API.  There are several options:
* [[OpenStack CLI]] - Open (similar to the swift client used in the [[Object Storage]] part of the tutorial)
* [[Python SDK]] - A unified front for the various OpenStack service clients
* [[REST API]] - writing your application to communicate directly with the OpenStack RESTful API endpoints
* [[Python Service Clients]] - using the OpenStack Python libraries (or libraries that may exist for other languages) to integrate OpenStack into your application


The steps in this section may be performed from any machine with Internet access. You will need to install packages on the machine, which depending on your setup might require either admin access, or installing them in a local environment.  We suggest using the OpenStack instance you launched in the earlier part of the tutorial, since it gives you a clean environment to start from.

This section will assume you have some familiarity with Python.  If not, there are a number of excellent tutorials around the web. The official Python.org [Getting Started](https://www.python.org/about/gettingstarted/) page is a good jumping-off point.

## Setting up API Access
Navigate: Project -> Compute -> Access & Security.  Choose the "API Access" tab under the Access & Security header.

[[tutorial_screenshots/liberty/api_access.png| API Access]]

You will see a list of URL endpoints for each OpenStack service.  When using the CLI or Python client libraries these are typically provided to your client script during the process of Keystone authentication.

Click "Download OpenStack RC File".  If you are going to be interacting with the Object Store using an s3 interface, you will also want to click "Download EC2 Credentials".  Save the resulting files to the local disk where you will be running the script.

## Install required packages

In this tutorial we will use Python, which allows us to use the native Python bindings for OpenStack APIs.  However there are many libraries available for other language, with varying levels of development activity and support.  You can read more about these here: [Software Development Kits](https://wiki.openstack.org/wiki/SDKs). 

Detailed instructions on installing openstack clients can be found in the End User Guide page on [command line clients][1].

The official instructions recommend using pip to install the clients, but if for some reason you need to avoid pip, you may be able to install the packagages from your OS package manager.  For example the Red Hat/CentOS client package is `python-openstackclient`.

Each OpenStack service has its own client.  However, current and future OpenStack releases are moving toward a single, unified client.  You should start by installing this common client:

    # pip install python-openstackclient

The purpose of this client is to centralize functionality so users do not have to remember different syntax for the CLI of each service.  It covers a pretty big slice of the available OpenStack functionality, but there are a few things it can't handle yet.  You may find that you need advanced features which are still only available from the individual service clients.  (This is already a rare case, and as time progresses, it will become rarer.)

The individual service clients are dependencies of python-openstackclient, but if you ever need to install an individual client, the syntax is:

    # pip install python-$PROJECTclient

where $PROJECT is the service name, such as `cinder`, `nova`, or `neutron`. The full list of clients can be found in the [guide][1], although keep in mind that not all the listed services are offered in the MOC OpenStack installation.

***

#### Next: [[OpenStack CLI]]
##### Previous: [[Object Storage]]
[[Openstack Tutorial Index]]
