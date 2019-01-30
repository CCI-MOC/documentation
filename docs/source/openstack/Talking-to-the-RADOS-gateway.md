<a name="top"></a>

* [Using Swift](#swift)
* [Using Curl](#curl)
* [Using S3 with python-boto](#python-boto)


## <a name="swift"></a>Using Swift

Install a swift command line client, such as python-swiftclient:

     $ sudo yum install python-setuptools
     $ sudo yum easy_install pip
     $ sudo pip install --upgrade setuptools
     $ sudo pip install --upgrade python-swiftclient

This allows you to use the command line syntax:

     $ swift -A http://[ENDPOINT_IP]/auth/1.0 -U [USER:SUBUSER] -K [KEY] [COMMAND]

There are slight differences to the syntax depending on whether you are using the radosgw's built-in authentication, or Keystone authentication.

#### Keystone authentication

Assuming $KEYSTONE_ENDPOINT, $OS_PROJECT, $OS_USERNAME, and $OS_PASSWORD are all set with your openstack credentials:

     $  swift -V 2.0 -A $KEYSTONE_ENDPOINT -U $OS_PROJECT:$OS_USER -K $OS_PASSWORD list

The output should show a list of containers in your account.

This longer syntax also works:

     $ swift -V 2.0 --os-auth-url $KEYSTONE_ENDPOINT --os-username $OS_USER --os-password $OS_PASSWORD  --os-tenant-name $OS_PROJECT list

#### Without Keystone Authentication

If you are using accounts created using the radosgw-admin CLI interface, you need to use the radosgw address and your user:subuser and key credentials

     $ swift -A http://rdgw.kaizen.massopencloud.org/auth/1.0 -U kamfonik:swift -K "ABCDEFG1234567890" list

will output:

     testbucket
     testbucket2

(assuming the subuser kamfonik:swift has these two top-level buckets)

To see what's in a bucket, add the bucket name to the end of the command:

     $ swift -A http://129.10.3.96/auth/1.0 -U kamfonik:swift -K "ABCDEFG1234567890" list testbucket

which outputs

     sub_testbucket

Run `man swift` to see what other commands are used for creating, deleting, and manipulating objects.

[top](#top)

***

## <a name="curl"></a>Using Curl
You can also talk to the radosgw without a swift client, using curl.  

### Keystone Authentication

First, get a token from keystone:

     $ AUTH_DATA='{"auth": {"tenantName": "'"$OS_PROJECT_NAME"'", "passwordCredentials": {"username": "'"$OS_USERNAME"'", "password": "'"$OS_PASSWORD"'"}}}'
     $ curl -i -H "Content-Type: application/json" -d "$AUTH_DATA" $OS_AUTH_URL/tokens

(The quotes around $AUTH_DATA are important, otherwise you will get an error that your JSON is not valid.)
    
Get the token ID from the response JSON.  Use the token ID to authenticate to the radosgw directly:
  
     $ MYTOKEN=<put the token ID string here>
     $ curl -X GET -H "x-auth-token: $MYTOKEN" http://rdgw.kaizen.massopencloud.org/swift/v1

The above GET command outputs a list of top-level containers.  More info about the different API calls and how to construct them can be found in the official [Ceph Documentation](http://docs.ceph.com/docs/master/radosgw/swift/).
  
You could do something similar with Python's requests library or similar as needed.


### <a name="curl-auth"></a>Authentication without Keystone

Before running any commands, you must authenticate with the gateway:

    $ curl -i -H "X-Auth-User: [USER:SUBUSER]" -H "X-Auth-Key: [KEY]" [URL]

Example:

     $ curl -i -H "X-Auth-User: kamfonik:swift" -H "X-Auth-Key: LNL+8e8K8H8hCxajNovT0FWmFwZxnENo3nMFPHcX" http://129.10.3.96/auth/v1.0

The reply will be a storage URL (X-Storage-URL) and an authorization token (X-Auth-Token):

     HTTP/1.1 204
     Date: Thu, 05 Nov 2015 22:45:35 GMT
     Server: Apache/2.4.6 (Red Hat Enterprise Linux)
     X-Storage-Url: http://129.10.3.96/swift/v1
     X-Storage-Token: AUTH_rgwtk0e0000006b616d666f6e696b3a7377696674e5c40bcea38082df8f2d3d56c07ee422bd169b60c15b868e65d569c9371569331e2620b0
     X-Auth-Token: AUTH_rgwtk0e0000006b616d666f6e696b3a7377696674e5c40bcea38082df8f2d3d56c07ee422bd169b60c15b868e65d569c9371569331e2620b0
     Content-Type: application/json

For the rest of the steps, you will direct your commands to this URL, and use this token to authenticate.  For convenience, you can put the url and token in variables:

     MYURL="http://129.10.3.96/swift/v1"
     MYTOKEN="AUTH_rgwtk0e0000006b616d666f6e696b3a7377696674e5c40bcea38082df8f2d3d56c07ee422bd169b60c15b868e65d569c9371569331e2620b0"

Note: the token may expire after an interval and you will need to authenticate again to get a new one.

Here are a few examples of commands and their output:

##### <a name="curl-create"></a>Create Bucket with Curl

      $ curl -i -X PUT -H "X-Auth-Token: $MYTOKEN" "$MYURL/testbucket2"

Output: 
      
     HTTP/1.1 201
     Date: Thu, 05 Nov 2015 22:46:49 GMT
     Server: Apache/2.4.6 (Red Hat Enterprise Linux)
     Accept-Ranges: bytes
     Content-Length: 0
     Content-Type: text/plain; charset=utf-8


##### <a name="curl-list"></a>List Buckets with Curl

      $ curl -i -X GET -H "X-Auth-Token: $MYTOKEN" "$MYURL"

Output:

     HTTP/1.1 200
     Date: Thu, 05 Nov 2015 23:08:08 GMT
     Server: Apache/2.4.6 (Red Hat Enterprise Linux)
     Transfer-Encoding: chunked
     Content-Type: text/plain; charset=utf-8
     
     testbucket
     testbucket2


##### <a name="curl-delete"></a>Delete Buckets with Curl

     $ curl -i -X DELETE -H "X-Auth-Token: $MYTOKEN" "$MYURL/testbucket2"
     HTTP/1.1 201
     Date: Thu, 05 Nov 2015 Nov 2015 23:16:49 GMT
     Server: Apache/2.4.6 (Red Hat Enterprise Linux)
     Accept-Ranges: bytes
     Content-Length: 15
     Content-Type: text/plain; charset=utf-8


##### <a name="sub"></a>Sub-Buckets with Curl
To create, list, or delete lower-level buckets, append name(s) of the outer bucket(s) to the storage URL:

     $ curl -i -X GET -H "Content-Length: 0" -H "X-Auth-Token: $MYTOKEN" "$MYURL/testbucket"
     HTTP/1.1 200
     Date: Thu, 05 Nov 2015 Nov 2015 23:22:42 GMT
     Server: Apache/2.4.6 (Red Hat Enterprise Linux)
     Accept-Ranges: bytes
     Content-Length: 15
     Content-Type: text/plain; charset=utf-8
     
     sub_bucket_test

## <a name="python-boto"></a>Using Amazon S3 interface with Python-Boto

### With Keystone Authentication
**Coming Soon!**

### Without Keystone Authentication

First, install python-boto:

     $ sudo yum install python-boto

Then, create a test script called "s3test.py" with these contents:

	import boto
	import boto.s3.connection
	access_key = [S3 ACCESS KEY]
	secret_key = [S3 SECRET KEY]

	conn = boto.connect_s3(
	aws_access_key_id = access_key,
	aws_secret_access_key = secret_key,
	host = '[RADOS GATEWAY FQDN]',
	is_secure=False,
	calling_format = boto.s3.connection.OrdinaryCallingFormat(),
	)

	bucket = conn.create_bucket('testbucket')

	for bucket in conn.get_all_buckets():
	        print "{name}\t{created}".format(
	                name = bucket.name,
	                created = bucket.creation_date,
	)

Run this script to create a bucket called testbucket, and list all buckets.

Script code for deleting testbucket would be:

     conn.delete_bucket('testbucket')