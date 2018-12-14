### Overview

This file explains how to do the `tempest.conf` configuration. The configuration itself has a lot of comment and direction on it and pretty self explanatory. This is just to clear possible confusions. 

### Tenant Isolation 
  * This is the dynamic credential creation method that allows running Tempest tests in parallel 
  * It creates separate tenants, networks and users for different classes of tests. 
  * It can create 3 types of users admin user, primary user, and alternative user. 
  * It's the most commonly use and easy of configuration
  * To configure this option 
    * [auth] - `use_dynamic_credentials = true`
    * [auth] - `create_isolated_networks = true`
    * [auth] - `admin_username`, `admin_tenant_name`, and `admin_password` to an admin credential that has permission to create users and tenants/projects. 

### [auth]

  This section in `tempest.conf` file is very well commented. Insert corresponding domain name, user credentials, and yaml account file from your cloud. 

### [compute] 

  `img_ref =` the uuid of an available image on the cloud, tests will create servers using this image. (recommend cirros) 

  `img_ref_alt =` uuid of an available image on the cloud, an alternative image tempest uses to create servers for tests. (This can be the same image in `img_ref`)

  `flavor_ref =` flavor id for server created from `img_ref`. This will be numbers (i.e. 1 or 2) it represents the flavor id

  `flavor_ref_alt =` flavor id for server created from `img_ref_alt`. (This has to be different than `flavor_ref`)

  `build_timeout = ` this is default 300 seconds, but sometime needs to be set to longer depends on the performance of the cloud's storage backend. 
   
  `region = ` the region of the cloud environment is in 

### [compute-feature-enabled]

  Follow the default options unless your cloud environment is supporting/not supporting that feature. For example if resize is enabled for the cloud set `resize = true`

  `api_extension = all`

  `change_password = false`

### [dashboard]

   `dashboard_url = ` The url of your cloud dashboard after login (e.g. https://my.cloud.com/)

   `login_url = ` The url of the login page on dashboard (e.g. https://my.cloud.com/auth/login/)

### [identity]

  `uri = https://my.cloud.com:5000/v2.0` 

  `auth_version = v2`

  `region = ` corresponding region

### [identity-feature-enable]

  Again this depends on the cloud environment. If Keystone v3 is enabled set `api_v3 = true`

  `api_v2 = true`

  `api_v3 = false`

  `api_extensions = all`

### [image]

  `build_timeout = ` this might need to be set to longer when you have issues with image is in active state but test failed with timeout error

### [image-feature-enable]

  `api_v2 = true` 

  `api_v1 = true`

### [negative]

  Use the default 

  `test_generator = tempest.common.generator.negative_generator.NegativeTestGenerator` generates negative tests 

### [network]

  `tenant_network_reachable = false` Because we choose `ssh_connect_method = floating`

  `public_network_id = ` this should be the uuid of the publicly reachable network, usually "public" on "admin" project

  `floating_network_name = ` **IMPORTANT** This is a lie. We see "Floating ip pool not found" error even with this specified. The floating network name (or floating network pool name) does NOT get passed to the tempest code. We have a hack around this which is to modify the `/root/tempest/.venv/lib/python2.7/site-packages/tempest_lib/common/rest_client.py` file. 

   we modified the content of `body` in this following function

```
def request(self, method, url, extra_headers=False, headers=None,
            body=None):
    ...
    ...
    ...
    import json
    if url == "os-floating-ips" and body == json.dumps({}):
        body = json.dumps({'pool': 'public'})
    resp, resp_body = self._request(method, url, headers=headers, body=body)
```

  `public_router_id = ` do not need to be specified, leave blank. Unless Neutron has `allow_overlapping_ips = false` in it's configuration

### [network-feature-enable]

  `ipv6 = false`

  `api_extensions = security-group, l3_agent_scheduler, ext-gw-mode, binding, quotas, agent, dhcp_agent_scheduler, provider, router, extraroute, external-net, allowed-address-pairs, extra_dhcp_opt, router, service-type, dvr` these are the api extensions that's provided in staging area, it should be identical to production cluster. 

### [scenario]

  This is the weird part. We never have great success with the scenario tests. Ideally scenario tests performs different use cases of OpenStack cloud using the API calls. It includes sshing into the instance which is also weird (will be explained in [validation] section). But to run scenario tests, you need to prepare a image directories (locally) that contains different formats of image files that will be used in the tests. The images can be found and downloaded from online.

```
img_dir = /root/cirros-img
img_file = cirros-0.3.2-x86_64-disk.img
ami_img_file = cirros-0.3.2-x86_64-blank.img
ari_img_file = cirros-0.3.2-x86_64-initrd
aki_img_file = cirros-0.3.2-x86_64-vmlinuz
```

### [service_available]

  Set according to what services are available in the cloud. 
  
### [stress]

  This requires access to the controller because it will look at the log files. I never did it. 

### [validation]
  
  I disabled it because it has tons of issues, I asked on IRC, and read about it on the bug reports. This option just doesn't work as it should. But this is also last Summer (June 2015). And they did have a lot of updates since then so it might work now? 

  `run_validation = ` boolean value to enable ssh on created servers and creation of additional validation resources to enable remote access. (Due to to the inconsistency of Tempest in handling ssh tests, disable this option for now)

  `connect_method = floating`

  `auth_method = keypair`

  `ip_version_for_ssh = 4`

  `image_ssh_user = cirros` if using cirros image

  `image_ssh_password = cubswin:)` if using cirros image

### [volume]

  `storage_protocol = ceph` for our backend. ceph has to be "ceph" it can NOT be "Ceph" of "CEPH"

  `vendor_name = Open Source` same as above, the vendor_name has to be "Open Source". 

### [volume-feature-enabled]

  `multi_backend = false`

  `backup = false`

  `snapshot = true`

  `api_extensions = all`

  `api_v1 = true`

  `api_v2 = true`

