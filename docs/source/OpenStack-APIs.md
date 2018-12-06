Links to the various openstack API docs:

Nova: http://docs.openstack.org/developer/python-novaclient/

Keystone: http://docs.openstack.org/developer/python-keystoneclient/

Cinder: http://docs.openstack.org/developer/python-cinderclient/

Swift: http://docs.openstack.org/developer/python-swiftclient/swiftclient.html

Neutron: http://docs.openstack.org/developer/python-neutronclient/

List of base operations we must provide:

Nova: vm operations (start/pause/stop/create/destroy), change vm config, list all instances, list my instances, list hosts, list flavors

Keystone: Query (users/endpoints/groups/roles/regions/policies), post new information, request confirmation on credentials. Not sure how it should tie in... most calls go ask for keystone token first?

Cinder: Create volume, delete volume, list volumes

Swift: Post/get/delete container (post/get/put object?)

Neutron: Create network, delete network, query available networks scoped to user, admin query all networks
