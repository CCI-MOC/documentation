# Using clouds.yaml with the OpenStack command line tools

Traditionally, the OpenStack command line tools were configured using a set of environment variables (`OS_AUTH_URL`, `OS_USERNAME`, etc.), usually delivered in the format of a simple shell script that can be sourced into your current environment. While this works, it becomes hard to manage if you are working with multiple OpenStack environments. The `clouds.yaml` configuration file was developed as an alternative mechanism for storing your OpenStack credentials. A `clouds.yaml` is written using [YAML][] syntax, and at a high level looks like this:

[YAML]: https://en.wikipedia.org/wiki/YAML

```
clouds:
  cloud1_name:
    ...credentials go here...
  cloud2_name:
    ...credentials go here...
```

See below for some specific examples.  The command line tools (and the Python API) look for `clouds.yaml` in the following locations (in this order):

- Your current directory
- `$HOME/.config/openstack/clouds.yaml`
- `/etc/openstack/clouds.yaml`

## Using `clouds.yaml`

Put your `clouds.yaml` file in one of the locations mentioned above (using `$HOME/.config/openstack/clouds.yaml` is probably the most convenient). To interact with a specific cloud, you may either set the `OS_CLOUD` environment variable, or specify a cloud on the command line using the `--os-cloud` option.

### Using the OS_CLOUD environment variable

This option is convenient if you are interacting with a single OpenStack environment.

```
$ export OS_CLOUD=kaizen
$ openstack flavor list
$ openstack server list
```

### Using the --os-cloud command line option

This option is convenient if you find yourself switching between multiple OpenStack environments.

```
$ openstack --os-cloud kaizen flavor list
$ openstack --os-cloud kaizen server list
```

## Examples

### Example 1: Application credentials

This example shows a `clouds.yaml` file used for authenticating to an OpenStack environment using [application credentials][]. This is what you'll be using if you authenticate using some form of single-signon.

[application credentials]: https://docs.openstack.org/keystone/queens/user/application_credentials.html

```
clouds:
  example2:
    auth_type: v3applicationcredential
    auth:
      auth_url: https://cloud.example.com:13000
      application_credential_id: 1234567890ABCDEF1234567890ABCDEF
      application_credential_secret: secret
    region: RegionOne
    interface: public
    identity_api_version: 3
```

### Example 2: Two clouds

This example shows a `clouds.yaml` that contains credentials for two different clouds environments.

```
clouds:
  cloud1:
    auth_type: v3applicationcredential
    auth:
      auth_url: https://cloud1.example.com:13000
      application_credential_id: 1234567890ABCDEF1234567890ABCDEF
      application_credential_secret: secret
    region: RegionOne
    interface: public
    identity_api_version: 3
  cloud2:
    auth_type: v3applicationcredential
    auth:
      auth_url: https:/cloud2.example.com:13000
      application_credential_id: FEDCBA0987654321FEDCBA0987654321
      application_credential_secret: secret
    region: RegionOne
    interface: public
    identity_api_version: 3
```

### Example 3: Username and password

This example shows a `clouds.yaml` file used for authenticating to an OpenStack environment using a username and password.

```
clouds:
  example1:
    auth:
      username: myusername
      project_name: myprojectname
      password: secret
      auth_url: https://kaizen.massopen.cloud:13000
      user_domain_name: Default
      project_domain_name: Default
    region: RegionOne
    interface: public
    identity_api_version: 3
```
