# Sensu and Filebeat for Ceph in Kaizen

The Kaizen production cluster's Ceph backend is provided by a Fujitsu appliance running custom software, and its internal networking is largely isolated.

By default SSH is disabled on the storage client network, which is the only direct connection to the Kaizen nodes.

The only other route in to the storage nodes is via the public IP of the admin node.

In order to collect Ceph metrics and logs with Sensu, an external virtual machine was set up called sensu-ceph.  This VM is running on Kaizen node 39.

The Ceph storage nodes run outdated versions of Ruby and Python, and the script which collects Ceph OSD metrics does not work there, so a division of labor is required.

One script runs on the storage nodes themselves to collect and print out the OSD data in JSON format.

This script is called by a second script which runs on sensu-ceph and is triggered by a Sensu check.

The second script collects the JSON output from each node and sends the appropriate metrics to influxDB.

### Setup of sensu-ceph
Sensu-ceph has the ip 10.13.37.215 on the Kaizen internal network, and it communicates with moc-sensu over this network.

This network also provides NAT, so the VM can reach the public IP of the ceph admin node.

Sensu and Filebeat are installed here and a public/private keypair is generated in `~sensu/.ssh/`

[This script](https://github.com/CCI-MOC/mocmon/blob/master/plugins/checkoutput_test.py) is placed on this node and called by a sensu check. The script connects to a storage node, calls a script there

Logs are copied from the individual storage nodes into the folder `/root/ceph_logs` every 6 hours, using rsync in a small bash script.

### Setup of Ceph Admin Node
The following iptables rules are added to the admin node:

     iptables -t nat -A PREROUTING -p tcp --dport 2221 -j DNAT --to-destination 192.168.20.11:22
     iptables -t nat -A PREROUTING -p tcp --dport 2222 -j DNAT --to-destination 192.168.20.12:22 
     iptables -t nat -A PREROUTING -p tcp --dport 2223 -j DNAT --to-destination 192.168.20.13:22
     iptables -t nat -A PREROUTING -p tcp --dport 2224 -j DNAT --to-destination 192.168.20.14:22
     iptables -t nat -A POSTROUTING -j MASQUERADE

These rules are also placed in a script `/root/scripts/sensu_iptables` which is run by `rc.local`, so that the rules are restored if the machine reboots.

### Setup of Ceph storage nodes 1-4
The user 'sensu' is created and the public key from sensu-ceph is added to `/home/sensu/authorized_keys`.

The following file is created in /etc/sudoers.d/sensu:
    
     Defaults:sensu !requiretty
     
     sensu ALL = (root) NOPASSWD: /usr/bin/python /home/sensu/collect-osd-metrics.py    

Note that the file MUST end with a newline.

The script [collect-osd-metrics.py](https://github.com/CCI-MOC/mocmon/blob/master/plugins/ceph-osd-metrics.rb) is placed in `/home/sensu`.

This setup must be performed on each of the four storage nodes.

