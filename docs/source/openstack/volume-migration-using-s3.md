# Volume migration use S3 API

## Overview

This article contains instructions for migration an OpenStack Cinder volume between two OpenStack environments (specifically, between the MOC Kaizen cluster and the NERC OpenStack cluster, but the instructions will work for any two OpenStack environments that have the necessary services enabled).

Broadly, the process looks like this:

1. Configure a `clouds.yaml` file with credentials for both environments
1. Create EC2 credentials for both environments
1. Create a backup of your volume on Kaizen
1. Use the MinIO client to copy the backup data to the NERC
1. Copy metadata for the backup from Kaizen to the NERC
1. Create a volume to receive the backup on the NERC
1. Restore the backup onto the new volume

## Prerequisites

### Create a clouds.yaml file

You will need a [`clouds.yaml`](clouds.yaml) file that contains application credentials for both Kaizen and the NERC.

- To create an application credential for Kaizen, see [this document](application-credentials).
- To create an application credential for the NERC, see [the NERC openstack CLI documentation](https://nerc-project.github.io/nerc-docs/openstack/advanced-openstack-topics/openstack-cli/openstack-CLI/).

In the remainder of this article, I'll assume you've named the two clouds `kaizen` and `nerc` in your `clouds.yaml` file.

### Install the OpenStack CLI

These instructions require use of the OpenStack command line client. Following the [installation guide][] to get the `openstack` command installed on your local system.

[installation guide]: https://docs.openstack.org/newton/user-guide/common/cli-install-openstack-command-line-clients.html

### Install MinIO client

The [MinIO client][]is a CLI tool for interacting with S3-compatible object storage services.  The MinIO documentation has instructions for installing the client on your local system.

[minio client]: https://docs.min.io/docs/minio-client-complete-guide.html

## Create EC2 credentials in your source and destination cloud environments

The S3-compatible API requires Amazon AWS-style credentials. We can generate these for each OpenStack environment by using the `ec2 credentials create` command.

Generating credentials for Kaizen:

```
$ openstack --os-cloud kaizen ec2 credentials create
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| Field      | Value                                                                                                                                         |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| access     | 1234567890ABCDEF1234567890ABCDEF                                                                                                              |
| links      | {'self': 'https://kaizen.massopen.cloud:13000/v3/users/ABCDEF0123456789ABCDEF0123456789/credentials/OS-EC2/1234567890ABCDEF1234567890ABCDEF'} |
| project_id | 690ce3e95fda4acb836912268aba0bd7                                                                                                              |
| secret     | 1234567890ABCDEF1234567890ABCDEF                                                                                                              |
| trust_id   | None                                                                                                                                          |
| user_id    | 9876543210ABCDEF0123456789ABDEF0                                                                                                              |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
```

Generate credentials for the NERC:

```
$ openstack --os-cloud nerc ec2 credentials create
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| Field      | Value                                                                                                                                         |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| access     | FEDCBA0987654321FEDCBA0987654321                                                                                                              |
| links      | {'self': 'https://stack.nerc.mghpcc.org:13000/v3/users/1234567890ABCDEF1234567890ABCDEF/credentials/OS-EC2/FEDCBA0987654321FEDCBA0987654321'} |
| project_id | 2a51155c78dd45bda77173548fb3c560                                                                                                              |
| secret     | 1234567890ABCDEF1234567890ABCDEF                                                                                                              |
| trust_id   | None                                                                                                                                          |
| user_id    | 1234567890ABCDEF1234567890ABCDEF                                                                                                              |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
```

In both cases, record the `access` and `secret` values, because we're going to need those in a subsequent step.

## Find object store endpoints

Look up information on the `object-store` service in each environment. We need to know the base URL for this service.

Information from Kaizen:

```
$ openstack --os-cloud kaizen catalog show object-store -c endpoints
+-----------+-------------------------------------------------------+
| Field     | Value                                                 |
+-----------+-------------------------------------------------------+
| endpoints | moc-kzn                                               |
|           |   admin: https://kzn-swift.massopen.cloud/swift/v1    |
|           | moc-kzn                                               |
|           |   public: https://kzn-swift.massopen.cloud/swift/v1   |
|           | moc-kzn                                               |
|           |   internal: https://kzn-swift.massopen.cloud/swift/v1 |
|           |                                                       |
+-----------+-------------------------------------------------------+
```

And from the NERC:

```
$ openstack --os-cloud nerc catalog show object-store -c endpoints
+-----------+----------------------------------------------------------------------------------------+
| Field     | Value                                                                                  |
+-----------+----------------------------------------------------------------------------------------+
| endpoints | regionOne                                                                              |
|           |   admin: http://10.255.2.253:8080                                                      |
|           | regionOne                                                                              |
|           |   internal: http://10.255.2.253:8080/v1/AUTH_01234567890123456789012345689012          |
|           | regionOne                                                                              |
|           |   public: https://stack.nerc.mghpcc.org:13808/v1/AUTH_01234567890123456789012345689012 |
|           |                                                                                        |
+-----------+----------------------------------------------------------------------------------------+
```

## Configure minio client

For each environment, create a MinIO alias configuration using the base URL of the "public" interface of the object-store service and the access key and secret your generated earlier:

```
$ mc alias set kaizen https://kzn-swift.massopen.cloud 1234567890ABCDEF1234567890ABCDEF 1234567890ABCDEF1234567890ABCDEF
$ mc alias set nerc https://stack.nerc.mghpcc.org:13808 FEDCBA0987654321FEDCBA0987654321 FEDCBA0987654321FEDCBA0987654321
```

## Create volume backup in source environment

Create a volume backup in kaizen:

```
$ openstack --os-cloud kaizen volume backup create --container <bucket_name> <volume id or name>
```

In the above command line, `<bucket_name>` is the name of the object storage container that will hold your backup. This must be unique across all users, so using your username as a prefix is probably a good idea (e.g., `--container larsks-backups`).

This will create a set of objects in the named bucket. You can use the MinIO client to inspect it:

```
$ mcli ls kaizen
[2022-03-17 13:48:47 EDT]     0B larsks-backups/
$ mcli ls kaizen/larsks-backups/
[2022-03-21 20:02:55 EDT]     0B volume_bb2086dc-d6a4-4346-9809-f47fbde673f3/
```

## Create target container in destination environment

We need to create a bucket in the target environment to receive the backup:

```
$ mcli mb nerc/larsks-backups
```

## Copy the backup

Use the MinIO client to copy the objects from the MOC environment to the NERC environment:

```
$ mcli mirror kaizen/larsks-backups/volume_bb2086dc-d6a4-4346-9809-f47fbde673f3 nerc/larsks-backups/volume_bb2086dc-d6a4-4346-9809-f47fbde673f3
```

## Copy backup record from source to destination

Now that we've copied the backup *data* into the NERC environment, we need to register the backup with the NERC backup service. We do this by copying metadata from Kaizen to the NERC:

```
$ openstack --os-cloud kaizen volume backup record export -f value > record.txt
$ openstack --os-cloud nerc volume backup record import -f value $(cat record.txt)
```

## Create target volume

Create a volume in the NERC environment to receive the backup. This must be the same size or larger than the original volume:

```
$ openstack --os-cloud nerc volume create myvolume
```

If this is a volume that will be used to boot a Nova server, include the `--bootable` flag:

```
$ openstack --os-cloud nerc volume create --bootable myvolume
```


## Restore backup

Finally, restore the backup onto the new volume:

```
$ openstack --os-cloud nerc volume backup restore mybackupid myvolume
```
