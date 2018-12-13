# PRB MOC Hass Master Config
This page documents the commands used to configure [moc-haas-master](HaaS-Development-Environment.html) in the [BU PRB HaaS Cluster](BU-PRB-Cluster.html)

### Networking
In this case, the system needs to act as a NAT router for the subnet. To do so, we use `firewalld.direct`, since we want to limit the NAT access only to the external interface.

These are the iptables rules that enable this:
```bash
iptables -I FORWARD -s 172.16.10.0/23 -i br-vlan0003 -o enp1s0 -j ACCEPT
iptables -I POSTROUTING -t nat -s 172.16.10.0/23 -o enp1s0 -j MASQUERADE
```

These were added to firewalld using:
```bash
firewall-cmd --permanent --direct --add-rule ipv4 nat POSTROUTING 0 -s 172.16.10.0/23 -o enp1s0 -j MASQUERADE
firewall-cmd --permanent --direct --add-rule ipv4 filter FORWARD 0 -s 172.16.10.0/23 -i br-vlan0003 -o enp1s0 -j ACCEPT
firewall-cmd --reload
```

### Forwarding SSH gateways
Ports 22222 and 22223 forward to the ssh-gateway (for accessing the MOC intranet) and bmi-ssh-gateway (for accessing the BMI special network) respectively. These port forwardings were accomplished using:

```bash
firewall-cmd --permanent --zone=public --add-forward-port=port=22222:proto=tcp:toport=22:toaddr=172.16.10.100
firewall-cmd --permanent --zone=public --add-forward-port=port=22223:proto=tcp:toport=22:toaddr=172.16.10.99
firewall-cmd --reload
```

### FreeIPA
To enroll a machine in FreeIPA, see [FreeIPA](FreeIPA.html)

