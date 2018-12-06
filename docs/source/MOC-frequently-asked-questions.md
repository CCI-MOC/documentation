#### Every time I spin up a new instance of ubuntu I am unable to connect to the internet without editing /etc/resolv.config and nameservers 8.8.8.8, 8.8.4.4. Is there a work around for this?

Put the DNS nameservers as specified in below links when creating/updating the private-network.

[Creating/updating private network](https://github.com/CCI-MOC/moc-public/wiki/Set-up-a-Private-Network)

[Specifying DNS nameservers](https://raw.githubusercontent.com/wiki/CCI-MOC/moc-public/tutorial_screenshots/create_network_details.png)

#### Unable to ssh/ping to my instance from outside world even though I have floating-ip assigned to it
Edit security-groups and allow ssh/ping to your instance. Unless you do that, you can't access those ports on your instance from outside.