Suppose your radosgw stores data in `.rgw.buckets` by default, but you want users to be able to specify a different pool called `target-pool`.

*Note: This walkthrough assumes 2 machines, a radosgw and a client of the radosgw.  Pay attention to which step is to be done on which machine.  If you are just testing radosgw functionality, it is possible to do the client steps on the same machine that is running the radosgw service.*

* [Create the placement target](#create-the-placement-target)
* [Using the placement target](#using-the-placement-target)

### Create the placement target

On the radosgw machine, get a copy of the current region and  zone configurations, and make backups:

     [root@radosgw ~]# radosgw-admin region get > region_conf.json
     [root@radosgw ~]# cp region_conf.json region_conf.json.BKP
     [root@radosgw ~]# radosgw-admin zone get > zone_conf.json
     [root@radosgw ~]# cp zone_conf.json zone_conf.json.BKP

  
Open region_conf.json in your favorite text editor.  Find the placement targets configuration, which by default looks like this:

    "placement_targets": [
        {
            "name": "default-placement",
            "tags": []
        }
    ],

Add a new placement target to the list for your target pool.  We'll call it `custom-target`:

    "placement_targets": [
        {
            "name": "custom-target",
            "tags": []
        },
        {
            "name": "default-placement",
            "tags": []
        }
    ],

Now open zone_conf.json, and find the `placement pools` section, which looks something like this:

    "placement_pools": [
        {
            "key": "default-placement",
            "val": {
                "index_pool": ".rgw.buckets.index",
                "data_pool": ".rgw.buckets",
                "data_extra_pool": ""
            }
        }
    ]


Add an additional placement pool.  Make sure the 'key' field matches the name of the placement target you configured above, which was `custom-target`

     "placement_pools": [
        {
            "key": "custom-target",
            "val": {
                "index_pool": "target-pool.index",
                "data_pool": "target-pool",
                "data_extra_pool": ""
            }
        },im 
        {
            "key": "default-placement",
            "val": {
                "index_pool": ".rgw.buckets.index",
                "data_pool": ".rgw.buckets",
                "data_extra_pool": ""
            }
        }
    ]

You will need to create the pool `target-pool.index`.  Technically, it will work if you just use the default `.rgw.index pool` for both placement targets, but I'm not really sure what the implications are, so best to separate everything out.

Apply the new configuration and restart the radosgw:

     [root@radosgw ~]# radosgw-admin region set < region_conf.json
     [root@radosgw ~]# radosgw-admin zone set < zone_conf.json
     [root@radosgw ~]# radosgw-admin regionmap update           # this might take some time
     [root@radosgw ~]# systemctl restart ceph-radosgw

### Using the placement target

Currently only the S3 interface supports placement targets.  You can use the python-boto library for this. Install the python-boto package on the machine that will be your 
     [root@client ~]# yum install python-boto
     [root@client ~]# yum install python-keystoneclient

Make sure you are installing Boto 2.x (yum info python-boto should show the version number) - there is a newer version Boto 3 but it is very different and I haven't found any info on how to make it work with Radosgw.

In order to use the s3 interface your OpenStack user needs to create S3 credentials.  With the appopriate OS_USERNAME, OS_TENANT_NAME, OS_PASSWORD, OS_AUTH_URL variables set, run:
     
     [root@client ~]# openstack ec2 credentials create

Output should look like this:

     +------------+----------------------------------+
     | Field      | Value                            |
     +------------+----------------------------------+
     | access     | 123457890ABCDEF1234567890ABCDEFG |
     | project_id | <your openstack project ID>      |
     | secret     | 9876543210ZYXWVUTSRQP98767543210 |
     | trust_id   | None                             |
     | user_id    | <your openstack user ID>         |
     +------------+----------------------------------+

The access and secret fields are the relevant ones.  On the machine that is a client of the radosgw, create a python script s3test.py and assign these key strings to the appropriate variables:

     import boto
     import boto.s3.connection

     access_key = "<YOUR S3 ACCESS KEY>"
     secret_key = "<YOUR S3 SECRET KEY>"
     rgw_host = "<radosgw dns name>"

     conn = boto.connect_s3(
         aws_access_key_id = access_key,
         aws_secret_access_key = secret_key,
         host = rgw_host,
         is_secure=False,
         calling_format = boto.s3.connection.OrdinaryCallingFormat(),
     )

     # This will create a bucket called my_bucket in the custom_target placement pool
     bucket_name='my_bucket'
     bucket = conn.create_bucket(bucket_name, location=":custom-target")
  
     # This will print the names of all of your buckets:
     for bucket in conn.get_all_buckets():
         print "{name}\t{created}".format(
             name = bucket.name,
             created = bucket.creation_date,
         )

Run the script.  This is what the output would look like if you already had some other buckets called `bucket_1` and `bucket_2`:

     # python s3test.py
     bucket_1   2016-05-01T18:54:00.000Z
     bucket_2   2016-05-27T12:20:27.000Z
     my_bucket   2016-06-29T21:41:56.000Z

If you had no other buckets before, you would only see the line for `my_bucket`.

From the radosgw, check that your bucket was created in the correct pool.

      # radosgw-admin bucket stats --bucket=my_bucket
        {
            "bucket": "my_bucket",
            "pool": "target-pool",
            "index_pool": ".rgw.buckets.index",
            "id": "default.39837434.1",
            "marker": "default.39837434.1",
             < other output omitted >
        }

Notice the 'marker' field - we will see this again later.

##### Upload a small object (<5GB)
Create a dummy file called `dummy.txt`.  Then create another python script:

     import boto
     import boto.s3.connection
     from boto.s3.key import Key

     access_key = "<YOUR S3 ACCESS KEY>"
     secret_key = "<YOUR S3 SECRET KEY>"
     rgw_host = "<radosgw DNS name>"

     source_path='/path/to/file/dummy.txt'

     conn = boto.connect_s3(
         aws_access_key_id = access_key,
         aws_secret_access_key = secret_key,
         host = rgw_host,
         is_secure=False,
         calling_format = boto.s3.connection.OrdinaryCallingFormat(),
     )

         bucket_name='my_bucket'

         b=conn.get_bucket(bucket_name)

         # boto uses a 'key' object to keep track of s3 data.  

         mykey = Key(b)
         mykey.key = 'my_dummy_file'  #this will be the name of your store dobject
         mykey.set_contents_from_filename(source_path)
  
     
Run this script (there should be no output), and then from the radosgw check to see that 'my_dummy_file' appears inside your target-pool:

         [root@radosgw ~]# rados -p target-pool ls | grep dummy
         
You should see output like this:
         
         default.39837434.1_my_dummy_file

Where the `default.39837434.1` prefix matches the 'marker' field that was in the bucket stats you retrieved earlier.

##### Script for larger files (>5GB)
Here is a script that will handle files larger than 5GB by splitting them into multiple parts.  You will need to run `pip install FileChunkIO` if you do not already have the FileChunkIO library.        

        import math, os
        import boto
        import boto.s3.connection
        from boto.s3.key import Key
        from filechunkio import FileChunkIO

        access_key = "<YOUR ACCESS KEY HERE>"
        secret_key = "<YOUR SECRET KEY HERE>"
  
        rgw_host = "<radosgw DNS name>"
   
        bucket_name='my_bucket'
        source_path='/path/to/file.ext'
        source_size=os.stat(source_path).st_size

        # get bucket object
        b=conn.get_bucket(bucket_name)
    
        # connect to the service
        conn = boto.connect_s3(
             aws_access_key_id = access_key,
             aws_secret_access_key = secret_key,
             host = rgw_host,
             is_secure=False,
             calling_format = boto.s3.connection.OrdinaryCallingFormat(),
        )

        # Create a multipart upload request
        mp = b.initiate_multipart_upload(os.path.basename(source_path))
        chunk_size = 52428800
        chunk_count = int(math.ceil(source_size / float(chunk_size)))

        # Send the file parts, using FileChunkIO to create a file-like object
        # that points to a certain byte range within the original file. We
        # set bytes to never exceed the original file size.
        try:
             for i in range(chunk_count):
                  offset = chunk_size * i
                  bytes = min(chunk_size, source_size - offset)
                  with FileChunkIO(source_path, 'r', offset=offset,
                                 bytes=bytes) as fp:
                  mp.upload_part_from_file(fp, part_num=i + 1)
        except:
            #print error and abort upload so that no orphaned chunks are left taking up space
            print("Error:", sys.exc_info()[0])
            mp.cancel_upload()
            raise        
        
        # Finish the upload
        mp.complete_upload()


These are only basic examples of how to use python-boto, for more examples see:
* [Boto S3 Documentation](http://boto.cloudhackers.com/en/latest/ref/s3.html#)
* [Boto Tutorial](http://boto.cloudhackers.com/en/latest/s3_tut.html)
* [Ceph Docs - Python S3 Examples](http://docs.ceph.com/docs/master/radosgw/s3/python/)
