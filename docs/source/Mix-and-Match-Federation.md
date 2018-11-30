# Mix and Match Federation
This guide assumes the installation of Mix & Match using DevStack.

### Keystone to Keystone
To setup Keystone 2 Keystone federation we use the [ansible-k2k](https://github.com/knikolla/ansible-k2k) playbook. Install Ansible and follow the information in the README of the playbook.

Currently the playbook works only for Liberty, but George is preparing a pull request to make it work with Mitaka.

### Patched Nova
When run via DevStack, the source code for the OpenStack services is not installed in the system python `site-packages` directory. Instead, the source code for the services is cloned from git to `/opt/stack/`. 

Therefore, to substitute Nova with our own version is enough to replace `/opt/stack/nova` with our own patched version and then restart the services.

To restart the services enter the screen via `screen -r`, then press `CTRL + A` followed by `"` (`SHIFT + '`) to view a list of all the available terminal sessions. Enter the ones starting with `n-` such as `n-api` and press `CTRL + C` to stop the process, then `UP ARROW` followed by `ENTER` to rerun it.

### Patched Clients
To patch the clients like `python-novaclient` simply download the uninstall the original version via `pip uninstall python-novaclient` and then install the new one with `python setup.py install`.

### Network connectivity
Shared network between environments is vlan 3800, 172.31.192.0/18, will be allocated by /22 chunks per environment. Routing is handled by brocade fabric on Engage1 side, Nexus switch on Kumo side.

**Work in progress, might change**
```
172.31.192.0/22 - Engage1, Kumo, Kaizen, subnet bits are /18, /22 is informational for IP range.`

172.31.192.1 - E1/Brocade router virtual interface in vlan 3800`

172.31.192.2 - E1/moc-services interface in vlan 3800`

172.31.192.4 - Kumo/Nexus router virtual interface in vlan 3800`

192.168.67.253 - E1/Brocade router virtual interface in vlan 4050 (ceph)`

192.168.131.254 - E1/Brocade router virtual interface in vlan 4052 (openstack private)`

172.31.192.4 - Kumo/Nexus router virtual interface in vlan 3702 (openstack private)`
```

