This tutorial will guide you through installing the Mix and Match proxy on a VM with Devstack. The proxy allows you to pick and choose which service providers to use for the needed services. More information on the installation process can be found at http://mixmatch.readthedocs.io/en/latest/installation.html

## Installation
Clone the github repository and install dependencies in a virtual environment.
```
git clone https://github.com/openstack/mixmatch
virtualenv venv 
source venv/bin/activate
cd mixmatch
pip install -r requirements.txt 
python setup.py install 
```

## Editing the Mixmatch Configuration File
Create a directory called mixmatch in etc and copy the sample configuration into the newly created directory. 
```
sudo mkdir /etc/mixmatch
cp /home/ubuntu/mixmatch/etc/mixmatch.conf.sample /etc/mixmatch/mixmatch.conf 
sudo vi /etc/mixmatch/mixmatch.conf
```
Currently, only the Cinder and Glance services are supported. Make the following changes to the mixmatch.conf to correctly set up the endpoints of the relevant services. Also, make sure the password in the configuration file matches the one in your admin file.
```
[DEFAULT] 
service_providers=default
[auth]
auth_url="https://localhost/identity/v3"
[sp_default]
auth_url="https://localhost/identity/v3"
image_endpoint="http://localhost/image" 
```
To add or change a service provider, you must create an option group for each provider in mixmatch.conf. The option group must contain the service provider's name in the format [sp_<service_providers_name>], the URI for connecting to the notification messagebus, the keystone auth url, and the endpoints for each enabled service. Also, add the service provider's name to service_providers in [DEFAULT]. For example:
```
[sp_example]
sp_name=example
messagebus="rabbit://stackrabbit:stackqueue@localhost"
auth_url="http://localhost/identity/v3"
image_endpoint="http://localhost/image"
volume_endpoint="http://localhost:8776"
enabled_services=image, volume
```
Make the following changes or additions to your admin file. The admin file can be found at devstack/acccrc/admin/admin.
```
export OS_AUTH_URL="http://your_private_ip_address/identity/v3"
export OS_IDENTITY_API_VERSION=3
```

## Changing Openstack Endpoints to the Proxy
To set up the proxy for Glance, you must first delete the image endpoint. Then, create a new endpoint directed to the proxy.
```
openstack endpoint delete <glance_endpoint_id>
openstack endpoint create --region RegionOne --enable image public 'http://your_private_ip:5001/image'
```
To set up the proxy for Cinder, you must first delete three volume endpoints. Then, create three new endpoints directed to the proxy.
```
openstack delete <cinder_endpoint_id> 
openstack delete <cinderv2_endpoint_id>
openstack delete <cinderv3_endpoint_id>
openstack endpoint create --region RegionOne --enable volume public 'http://your_private_ip:5001/volume/v1/(project_id)s'
openstack endpoint create --region RegionOne --enable volumev2 public 'http://your_private_ip:5001/volume/v2/(project_id)s'
openstack endpoint create --region RegionOne --enable volumev3 public 'http://your_private_ip:5001/volume/v3/(project_id)s'
```

## Editing the Nova and Cinder Configuration Files
For Nova, make the following changes to nova.conf.
```
[glance]
api_servers=http://your_private_ip_address:5001/image 
```
For Cinder, make the following changes and additions to cinder.conf.
``` 
[default]
glance_api_servers=http://your_private_ip_address:5001/image 

[oslo_messaging_notifications]
driver = messaging
topics = notifications
```
Finally, restart the glance, cinder, and nova services.
```
sudo systemctl restart devstack@g-* 
sudo systemctl restart devstack@c-* 
sudo systemctl restart devstack@n-* 
```

## Running the Proxy
It is advised to run the proxy in a screen so that the proxy does not break if your ssh session dies.
```
screen -S proxy
source venv/bin/activate 
/home/ubuntu/mixmatch/run\_proxy.sh
```

