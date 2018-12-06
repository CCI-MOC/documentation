### Intro to Object Storage 

The Object Store is an interface where users can upload data directly to our Ceph cluster for storage.  Please note that while Ceph is a reliable, replicated storage solution, we do not currently maintain any offsite backup of user data.  Please do not keep your only copy of critical data in our Object Store.

You can manage objects in the Object Store via the web interface, or by using the API to connect to the Rados Gateway service, which provides a Swift or S3 interface.

### Web Interface
To get started, navigate to Project -> Object Store -> Containers.

[[tutorial_screenshots/liberty/object_store.png|Object Store]]

##### Create a Container
In order to store objects, you need at least one "Container" to put them in.  Containers are essentially top-level directories.  Other services use the terminology "buckets".  

Click Create Container.  Give your container a name.  The name needs to be unique, not just within your project but across all of our OpenStack installation.  If you get an error message after trying to create the container, try giving it a more unique name.  

For now, leave the Container Access set to Private.

[[tutorial_screenshots/mitaka/object_store_create_container.png|Create Container]]

Your container now appears in the containers list.

[[tutorial_screenshots/mitaka/object_store_container_list.png|Container List]]

##### Upload a File
Click on the name of your container, and click the Upload File icon.  Click Browse and select a file from your local machine to upload.  It can take a while to upload very large files, so if you're just testing it out you may want to use a small text file or similar.

By default the File Name will be the same as the original file, but you can change it to another name. Click "Upload File".  Your file will appear inside the container. 

[[tutorial_screenshots/mitaka/object_store_upload_file.png]]

##### Using Folders
Files stored by definition do not organize objects into folders, but you can use folders to keep your data organized.  On the backend, the folder name is actually just prefixed to the object name, but from the web interface (and most other clients) it works just like a folder.

To add a folder, click on the "+ folder" icon.

##### Make a container public
Making a container public allows you to send your collaborators a URL that gives access to the container's contents. Click on your container's name, then check the "Public Access" box. Note that "Public Access" changes from "disabled" to "link".

[[tutorial_screenshots/mitaka/object_store_make_public.png]]

Click "link" to see a list of object in the container. This is the URL of your container.  

Note that anyone who obtains the URL will be able to access the container, so this is not recommended as a way to share sensitive data with collaborators.  In addition, everything inside a public container is public, so we recommend creating a separate container specifically for files that should be made public.

In this example, we have added a folder and a second object, to show how the directory structure appears in the container.

[[tutorial_screenshots/mitaka/object_store_public_url.png|Container Public URL]]

To download the file `test.txt` we would use the following url:
`http://rdgw.kaizen.massopencloud.org/swift/v1/test-container-unique/test.txt`

Or you can just click on "Download" next to the file's name. 

[[tutorial_screenshots/mitaka/object_store_download_file.png]]

You can also interact with public objects using a utility such as curl:

    # curl http://rdgw.kaizen.massopencloud.org/swift/v1/tutorial_container_unique
    test_file
    tutorial_pseudo-folder/
    tutorial_pseudo-folder/test_file2

To download a file:
    # curl -o local_file.txt  http://rdgw.kaizen.massopencloud.org/swift/v1/tutorial_container_unique/test_file

##### Make a container private
You can make a public container private by clicking the dropdown next to the container and clicking Make Private.  This will deactivate the public URL of the container.

### API Access

You can also use a Swift or Amazon S3 client to interact with the object store.

##### Swift interface

There are a number of Swift clients available.  In this tutorial we will use python-swiftclient.

    # yum install python-swiftclient

If when trying to use the client, you get an error about ssl_match_hostname, you will need to manually install the relevant python backport:
 
    # yum install python-backports.ssl_match_hostname

Swift authenticates using a user, tenant, and key, which map to your OpenStack username, project, and password.

Set these environmental variables using your openstackrc file, and type the command below to get a lits of your containers:

    $ swift -V 2 -A $OS_AUTH_URL -U $OS_TENANT_NAME:$OS_USERNAME -K $OS_PASSWORD list
    tutorial_container_unique

To upload a file to tutorial_container_unique

    $ swift -V 2 -A $OS_AUTH_URL -U $OS_TENANT_NAME:$OS_USERNAME -K $OS_PASSWORD upload tutorial-container_unique /path/to/file
    tutorial_container_unique

Type "man swift" to learn more about using the swift commands.  The client has a --debug flag, which can be useful if you are having issues.

***
#### Next: [[API Access]]
###### Previous:  [[Volumes]]   
[[Openstack Tutorial Index]]  