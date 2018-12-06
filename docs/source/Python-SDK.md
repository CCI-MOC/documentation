<!-- linky links -->
[Python SDK page at PyPi]: https://pypi.python.org/pypi/openstacksdk
[Openstack End User Guide]: http://docs.openstack.org/user-guide/
[1]: http://docs.openstack.org/user-guide/common/cli-install-openstack-command-line-clients.html
[known SDKs]: https://wiki.openstack.org/wiki/SDKs

## Intro

From the [Python SDK page at Pypi]:  
> The python-openstacksdk is a collection of libraries for building applications to work with OpenStack clouds. The project aims to provide a consistent and complete set of interactions with OpenStack’s many services, along with complete documentation, examples, and tools."

If you need to plug OpenStack into existing scripts using another language, there are a variety of other SDKs at various levels of active development.  A list of [known SDKs] is maintained on the official OpenStack wiki.	 

## Hello World script

Let's jump right in with an example.  Here's a script equivalent of the `openstack image list` command we performed via the CLI on the [previous page](#API-Access.md)

    import os
    from openstack import connection
    from openstack import profile
    
    
    # There are several ways to set these variables in Python.  In this example, we
    # assume you sourced the Openstack RC script and get the correct values from the
    # environement. You could also read them into the script from a config file.
    auth_url = os.environ.get('OS_AUTH_URL')
    project_name = os.environ.get('OS_PROJECT_NAME')
    username = os.environ.get('OS_USERNAME')
    password = os.environ.get('OS_PASSWORD')
    region = os.environ.get('OS_REGION_NAME')
    
    def create_connection(auth_url,region,project_name,username,password):
        """ Connect and authenticate to OpenStack
     
        This function came straight from the Openstack docs:
        http://developer.openstack.org/sdks/python/openstacksdk/users/guides/connect.html
        """
        prof = profile.Profile()
        prof.set_region(profile.Profile.ALL, region)
        return connection.Connection(
            profile=prof,
            user_agent='examples',
            auth_url=auth_url,
            project_name=project_name,
            username=username,
            password=password)
    
    
    conn = create_connection(auth_url,region,project_name,username,password)
    for image in conn.compute.images():
         print "{ID}\t{name}".format(ID=image.id, name=image.name)


Wait, you're thinking, that looks like a lot more work than `openstack image list`!  But, look at how much of that work is just the openstack authentication step.  You only have to do that part once per script.

Notice that we access the image name and id.  There's actually a lot more information we can get from the output of conn.compute.images().  To see an example, add this to the bottom of your script:

    image1 = conn.compute.images().next()
    print(image1)

Pro Tip: if you'd like the output of the above to be a bit more readable, you can do this:

    import json
    import pprint
    
    img_dict = img.to_dict()
    pp = pprint.PrettyPrinter(indent=4)
    pp.pprint(img_dict)


Most OpenStack resource objects have this `to_dict()` function, since they inherit it from the base Resource class.

Here's an [example script](tutorial_scripts/list-images.py) that makes use of the code above.  You need to make sure your environment variables are set in order for it to work.

## Launch an instance

OK, enough looking at images, let's get some work done and launch an instance. 

Make a copy of your list-images script called something like launch.py.  Remove the for loop that lists images (this is the last two lines).

Define some instance parameters like this:

    INSTANCE_NAME='Python Test Image'
    IMAGE='Centos 7'
    FLAVOR='m1.small'
    NETWORK='your-network-here'
    KEYPAIR='your-keypair-here'

Fill in appropriate values for these parameters based on what's available in the OpenStack installation, and the image configuration you want.  You can specify image, flavor, network, and keypair by either name or ID value, but make sure it's a string. If you are a member of multiple projects, make sure to provide a network and keypair that are in the project specified in your openstackrc file.

Next, use these values to prepare some resources:

    # Get resources
    img = conn.compute.find_image(IMAGE)
    flavor = conn.compute.find_flavor(FLAVOR)
    network = conn.network.find_network(NETWORK_NAME)
    keypair = conn.compute.find_keypair(KEYPAIR)

Finally, create your instance:

    test_instance = conn.compute.create_server(
        name='testPython2', 
        image_id=img.id, 
        flavor_id=flavor.id, 
        networks=[{"uuid": network.id}], 
        key_name=keypair.name)
    
    print(test_instance)

Run your script: 
    $ python launch.py  

It should print the attributes of your server, and you should see your new instance appear in the Instances list in the Horizon GUI.

An [example script](tutorial_scripts/sdk_launch.py) is available for download - make sure to fill in the appropriate image, flavor, keypair, and network names.

***

#### Next: [[REST API]]
##### Previous: [[OpenStack CLI]]
[[OpenStack Tutorial Index]]






