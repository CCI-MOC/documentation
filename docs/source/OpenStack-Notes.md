To force usage of keystone's v3 API:
* Download openrc
* Adjust OS_AUTH_URL to use "v3" instead of "v2.0". From:
 * `export OS_AUTH_URL=http://192.168.100.100:5000/v2.0`
 * to
 * `export OS_AUTH_URL=http://192.168.100.100:5000/v3`

* Pass the "--os-identity-api-version=3" flag to the openstack command:
 * `openstack  --os-identity-api-version=3 server list`
 * Other commands (like nova) may not be updated to use the v3 keystone authentication API.

To receive DHCP packets in a VM under KVM (for instance, as one would want to do for a FUEL server):
* One must disable iptables from running on the bridge interface.
 * Add `net.bridge.bridge-nf-call-iptables = 0` to /etc/sysctl.conf
 *  http://wiki.libvirt.org/page/Networking
