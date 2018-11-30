# HaaS Development Environment
There are 3 haas environments in PRB. Nitty gritty network cabling and other info can be found in [[PRB HaaS Cluster]].

### SSH gateway to MOC Intranet

  **Host:** moc-haas-master.bu.edu port 22222

The MOC Intranet is isolated from the Internet so that we can be a little more open about the haas services. To access the Intranet, one can use the ssh gateway by ssh'ing to: `moc-haas-master.bu.edu` port `22222`. If you are a haas dev, you can also access the DEV and DEPLOY environments directly from the Internet.

### HaaS envs

  **BETA** : haas-beta.prb.massopencloud.org (only accessible from within the MOC Intranet)

Location for users to try the latest/greatest haas bits on machines that aren't as high class as the production ones.

Machines placed on the moc-intranet network can reach the outside world by following the network config info found on [[PRB HaaS Cluster]].

  **DEV** : [moc-haas-master.bu.edu](PRB-MOC-Haas-Master-Config.html) (accessible from Internet)

This is for active development/testing. HaaS is to be run using `haas serve`/`haas serve_networks`. If someone else is using, talk to them and ask them to kill their processes.

  **DEPLOY**: moc-haas-deploy.bu.edu (accessible from Internet)

This server is reserved for running the deployment tests.

