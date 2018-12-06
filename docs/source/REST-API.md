<!-- linky links -->
[OpenStack API Guide]: https://docs.openstack.org/api/api-ref-guides.html
## Introduction 

It's possible to write your application to interact directly with OpenStack's REST APIs.  This requires some comfort with REST but in some cases may be the best way to adapt your particular application to make use of a particular service.

An introduction to the concept of REST and how to use it is beyond the scope of this tutorial, but we do provide an example below to get started with.

A good resource is the [OpenStack API Guide].

## Example

Note that the script below includes both Keystone V2 and V3 authentication.  We recommend working with V3 auth for new applicatoins, but include the V2 example to illustrate the differences for those who might be trying to work with an existing V2 application.  

Our current Mitaka production environment supports both APIs (as of this writing, April 2017).

To run this script you must specify which authentication method to use:

     $ python REST_example.py --v3
or

     $ python REST_example.py --v2

```
import argparse
import json
import os
from keystoneauth1.identity import v2, v3
from keystoneauth1 import session


if __name__ == "__main__":

    # Code block below sets up--v2 and --v3 arguments to this script.
    parser = argparse.ArgumentParser(description='Specify v2 or v3 auth')
    version = parser.add_mutually_exclusive_group(required=True)
    version.add_argument('--v2', action='store_true',
                         help='use Keystone auth v2')
    version.add_argument('--v3', action='store_true',
                         help='use Keystone auth v3')
    args = parser.parse_args()

    # In this example, we assume you sourced the Openstack RC script and
    # read these values from the environement. 
    project_id = os.environ.get('OS_TENANT_ID')
    project_name = os.environ.get('OS_PROJECT_NAME')
    user_name = os.environ.get('OS_USERNAME')
    password = os.environ.get('OS_PASSWORD')
    # Note that the environment variable OS_AUTH_URL set in the currrently 
    # available RC scripts specifies v2.0. Here we split off the version 
    # so that in this script we can choose v2 or v3.
    keystone_base = os.environ.get('OS_AUTH_URL').rsplit('/', 1)[0]

    if args.v2:
        auth = v2.Password(auth_url='{}/v2.0'.format(keystone_base),
                           tenant_name=project_name,
                           username=user_name,
                           password=password)
        
    elif args.v3:
        auth = v3.Password(auth_url='{}/v3'.format(keystone_base),
                           project_name=project_name,
                           project_domain_id='default',
                           username=user_name,
                           user_domain_id='default',
                           password=password)

    # Establish an authenticated session
    sess = session.Session(auth=auth)
import argparse
import json
import os
from keystoneauth1.identity import v2, v3
from keystoneauth1 import session


if __name__ == "__main__":

    # Code block below sets up--v2 and --v3 arguments to this script.
    parser = argparse.ArgumentParser(description='Specify v2 or v3 auth')
    version = parser.add_mutually_exclusive_group(required=True)
    version.add_argument('--v2', action='store_true',
                         help='use Keystone auth v2')
    version.add_argument('--v3', action='store_true',
                         help='use Keystone auth v3')
    args = parser.parse_args()

    # In this example, we assume you sourced the Openstack RC script and
    # read these values from the environement. 
    project_id = os.environ.get('OS_TENANT_ID')
    project_name = os.environ.get('OS_PROJECT_NAME')
    user_name = os.environ.get('OS_USERNAME')
    password = os.environ.get('OS_PASSWORD')
    # Note that the environment variable OS_AUTH_URL set in the currrently 
    # available RC scripts specifies v2.0. Here we split off the version 
    # so that in this script we can choose v2 or v3.
    keystone_base = os.environ.get('OS_AUTH_URL').rsplit('/', 1)[0]

    if args.v2:
        auth = v2.Password(auth_url='{}/v2.0'.format(keystone_base),
                           tenant_name=project_name,
                           username=user_name,
                           password=password)
        
    elif args.v3:
        auth = v3.Password(auth_url='{}/v3'.format(keystone_base),
                           project_name=project_name,
                           project_domain_id='default',
                           username=user_name,
                           user_domain_id='default',
                           password=password)

    # Establish an authenticated session
    sess = session.Session(auth=auth)

    # A full list of available public API endpoints is at:
    #     Compute --> Access & Security --> API Access
    # Some will need to include your project ID, some will not
    nova_url = 'https://nova.kaizen.massopencloud.org:8774/v2/{}'.format(projectt
_id)
    glance_url = 'https://glance.kaizen.massopencloud.org:9292'

    # Here we retrieve a list of available images
    # Since the glance endpoint does not include a version, we discover 
    # the current version
    # More information about service discovery can be found here:
    # https://docs.openstack.org/developer/keystoneauth/using-sessions.html#servv
ice-discovery
    # Note that the below discovery does not work for 
    response = sess.get(glance_url)
    glance_versions = json.loads(response.text)
    current_glance = (version for version in glance_versions['versions']
                      if version['status'] == 'CURRENT').next()
    glance_url = current_glance['links'][0]['href']
    response = sess.get('{}/images'.format(glance_url))
    image_list = json.loads(response.text)
    for image in image_list['images']:
        print image
        print "******"

    # Here we retrieve a list of the instances in your project
    # Since the Nova API endpoint already includes a version, we
    # can just use it directly
    response = sess.get('{}/servers'.format(nova_url))
    server_list = json.loads(response.text)
    for server in server_list['servers']:
        print server
        print "******"
```

Download the above script: [REST_example.py](tutorial_scripts/REST_example.py)
***

#### Next: [[Python Service Clients]]
##### Previous: [[Python SDK]]
[[OpenStack Tutorial Index]]