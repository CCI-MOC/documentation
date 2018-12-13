The script below is an example of how to populate the HaaS with nodes.  
It also configures the switch accordingly. This is the script that was 
used to add the extra rack of servers into the northeastern cluster's 
HaaS.

```python
import json
import pexpect
import requests

conn = pexpect.spawn('ssh admin@10.99.1.5')
conn.expect('Password')
conn.sendline('CENSORED')
conn.expect('MOC-NEU')
conn.sendline('config terminal')

endpoint = 'http://127.0.0.1'

for node in range(1, 25):
    if node == 6:
        # People are still using the foreman node, so we'll wait until
        # it's a good time for some downtime to add this.
        continue

    # Detach the node from all networks; HaaS doesn't do this itself.
    conn.sendline('int ethernet 1/%d' % node)
    conn.sendline('sw mode trunk')
    conn.sendline('sw trunk native vlan 2500')
    conn.sendline('sw trunk allowed vlan none')
    conn.sendline('exit')

    # Register the node with HaaS
    requests.put(endpoint + '/node/cisco-%02d' % node,
                 data=json.dumps({
                     'ipmi_host': '10.99.1.%d' % (node + 100),
                     'ipmi_user': 'admin',
                     'ipmi_pass': 'CENSORED',
                 }))

    # Register the corresponding nic and port, and connect them:
    requests.put(endpoint + '/switch/nexus-1/port/ethernet%201%2F' + str(node))
    requests.put(endpoint + '/node/cisco-%02d/nic/enp130s0f0' % node, data=json.dumps({
        "macaddr": "UNKNOWN",
    }))
    requests.post(endpoint + '/switch/nexus-1/port/ethernet%201%2F' +
                  str(node) +
                  '/connect_nic',
                  data=json.dumps({
                      "node": "cisco-%02d" % node,
                      "nic": "enp130s0f0",
                  }))

# activate all of the allocation pool's networks.
for vlan in range(1000,1100):
    conn.sendline('vlan %d' % vlan)
    conn.sendline('state active')
    conn.sendline('exit')
```
