## MOC FAQs

---

Every time I spin up a new instance of ubuntu I am unable to connect to the internet without editing 
`/etc/resolv.config` and setting `nameservers 8.8.8.8, 8.8.4.4`. 

Put the DNS nameservers as specified in below links when creating/updating the private-network.

[Creating/updating private network](../openstack/Set-up-a-Private-Network.html)

![](../_static/img/create_network_details.png)

---

Unable to ssh/ping to my instance from outside world even though I have floating-ip assigned to it

Edit security-groups and allow ssh/ping to your instance. Unless you do that, you can't access those ports on your instance from outside.
