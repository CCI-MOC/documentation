## Using linux bridge
Here are the steps for adding 2nd nic to the instances:

Step1:-

Create bridge on compute nodes.
```
brctl addbr br-cache
ifconfig br-cache up
```
Configure physical interface (used to connect to cache nodes) to be a part of this bridge (br-cache).

Step2:-

Apply the patch given below:-

```
File: /usr/lib/python2.7/site-packages/nova/virt/libvirt/driver.py

Patch:
26a27
> 
40,41d40
< import random
< import string
115d113
< 
4508,4527d4505
<     def _getRandMac(self, prefix):
<         for i in range(0, 3):
<             prefix += ":" + format(random.randint(0, 255), 'x')
<         return prefix
< 
<     def _getDevName(self):
<         id = ''.join(random.choice(string.lowercase) for i in range(8))
<         return "cache-" + id
< 
<     def _cacheIntfXML(self, prefix):
<         mac = self._getRandMac(prefix)
<         dev = self._getDevName()
<         x = '\n  <interface type="bridge">'\
<            +'\n    <mac address="%s"/>'\
<            +'\n    <model type="virtio"/>'\
<            +'\n    <source bridge="br-cache"/>'\
<            +'\n    <target dev="%s"/>'\
<            +'\n  </interface>'
<         return x % (mac, dev)
< 
4549d4526
<         LOG.debug(conf)
4551,4556d4527
<         cache_interface = etree.XML(self._cacheIntfXML("fa:17:3e"))
<         main_xml = etree.XML(xml)
<         devices = main_xml.find("devices")
<         interfaces = devices.find("interface")
<         interfaces.addnext(cache_interface)
<         xml = etree.tostring(main_xml)
```

Step3:-

Once the instances are booted up, assign ip-address in cache-subnet to relevant interface on those VMs.
Alternatively, DHCP server can be configured to run in that subnet and everyone will get ip-address from DHCP server.

## Using openvswitch for basic communication
Step1:-

Create vswitch on compute nodes.
```
ovs-vsctl add-br br-cache
```

Step2:-

Apply the same patch, but just modify the _cacheIntfXML(self, prefix) method to this:
```
    def _cacheIntfXML(self, prefix):
        mac = self._getRandMac(prefix)
        dev = self._getDevName()
        x = '\n  <interface type="bridge">'\
           +'\n    <virtualport type="openvswitch">'\
           +'\n    </virtualport>'\
           +'\n    <mac address="%s"/>'\
           +'\n    <model type="virtio"/>'\
           +'\n    <source bridge="br-cache"/>'\
           +'\n    <target dev="%s"/>'\
           +'\n  </interface>'
        return x % (mac, dev)
```
Restart openstack services. Now the instances will be a part of openvswitch.

