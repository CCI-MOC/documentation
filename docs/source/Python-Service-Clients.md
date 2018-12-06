<!-- linky links -->
[novaclient]: http://docs.openstack.org/developer/python-novaclient/     
[cinderclient]: http://docs.openstack.org/developer/python-cinderclient/     
[neutronclient]: http://docs.openstack.org/developer/python-neutronclient/      
[glanceclient]: http://docs.openstack.org/developer/python-glanceclient/     
[swiftclient]: http://docs.openstack.org/developer/python-swiftclient/swiftclient.html   
[OpenStack SDK docs]: https://docs.openstack.org/user-guide/sdk-overview.html#openstack-sdk
## Introduction
The individual service clients are Python libraries that wrap the OpenStack REST APIs.  According to the official [OpenStack SDK docs], this method of interacting with OpenStack should be avoided unless there is no other way to accomplish what you need.  We provide some working examples here in case this describes your use case.

## Hello World script

Let's jump right in with an example.  Here's a script that will list all instances in your project. 

    import os
    from novaclient import client as novaclient

    # There are several ways to set these variables in Python.  In this example, we
    # assume you sourced the Openstack RC script and get the correct values from the
    # environement. You could also read them into the script from a config file.
    auth_url = os.environ.get('OS_AUTH_URL')
    project_name = os.environ.get('OS_PROJECT_NAME')
    username = os.environ.get('OS_USERNAME')
    password = os.environ.get('OS_PASSWORD')
    region = os.environ.get('OS_REGION_NAME')
    
    
    nova_version = 2;
    
    nova = novaclient.Client(nova_version,
                             username,
                             password,
                             project_name,
                             auth_url) 

Here's an [example script](tutorial_scripts/api-client-examples.py) that shows how to set up clients for the different services and retrieve some basic information about existing resources.  You need to make sure your environment variables are set in order for it to work. 

## Launch an instance

Let's launch an instance from a script.  We'll assume that you have already created a private network, and that you know the name of the image you want to launch from.

Make a copy of the python-api-clients.py script, let's call the new script nova_launch.py.

Define some instance parameters like this:

    INSTANCE_NAME='Nova Test Launch'
    IMAGE_NAME='Centos 7'
    FLAVOR_NAME='m1.small'
    NETWORK_NAME='--network name here--'
    KEYPAIR='--keypair name here---'

Fill in appropriate values for these parameters based on what's available in the OpenStack installation, and the image configuration you want.

Next, set up the novaclient and use these values to prepare some resources:
    nova_version = 2
    
    nova = novaclient.Client(nova_version,
                             username,
                             password,
                             project_name,
                             auth_url)
    
    image = nova.images.find(name=IMAGE_NAME)
    flavor = nova.flavors.find(name=FLAVOR_NAME)
    network = nova.networks.find(label='test_network')
    keypair = nova.keypairs.find(name=KEYPAIR)
    
Note that novaclient shouldn't be used to manipulate networks or images - use neutronclient or glanceclient for that. 

This code is what will actually launch your instance:

    my_instance = nova.servers.create(INSTANCE_NAME, image, flavor, nics=[{'net-id': network.id}], key_name=keypair.name)
    
    print my_instance

Run your script from a terminal where you have sourced the openstackrc script:

    # python nova_launch.py

If you log into the Horizon GUI, you can see your instance.

The [example script](tutorial_scripts/nova_launch.py) is available for download - make sure to fill in the appropriate image, flavor, keypair, and network names, and to set your environment variables.


# OpenStack Python API Documentatoin

More information about the various OpenStack clients is available in the official documentation:

* [Nova][novaclient]
* [Neutron][neutronclient]
* [Cinder][cinderclient]
* [Glance][glanceclient]
* [Swift][swiftclient]


***

##### You have now reached the end of the tutorial! Good work!

***

##### Previous: [[REST API]]
[[OpenStack Tutorial Index]]






