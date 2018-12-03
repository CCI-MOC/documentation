# Registering Mirantis Images
Before you can launch a cluster, you must provide an image designed to be used with Sahara.

At present, compatible images are hosted by [Mirantis](http://sahara-files.mirantis.com/images/upstream/) Choose an image compatible with your OpenStack version, and with desired OS and Hadoop plugin.  

Upload the image by visiting Compute → Images → Create Image. 

Give your image a creative name, use the Mirantis URL as the image location, and select QCOW2 if necessary. Leave everything else blank, and submit the form. Now, wait for your image to be uploaded.

Register the image with Sahara in Data Processing → Image Registry → Register Image. Choose desired image from the dropdown, specify username, and add the correct plugin tags.

Usernames are generally `ubuntu` for Ubuntu images, `fedora` for Fedora, `cloud-user` for CentOS 6, and `centos` for CentOS 7.  

Alternatively, you may use the following [Python script](https://github.com/CCI-MOC/moc/tree/master/scripts/sahara_upload)

