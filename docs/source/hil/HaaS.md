## HIL
Formerly HaaS (Hardware as a Service)
 -  [HIL Repo](https://github.com/CCI-MOC/hil)
 -  [Bug Tracker](https://github.com/CCI-MOC/hil/issues)
 -  [HIL Interface](Haas-interface.html)
 -  [HIL Bootstrapping](HaaS-bootstrapping.html)

### Recursive HaaS
Requires modifying the following to pass through:
 -  Node allocate/free
 -  IPMI operations (reset, console)
 -  Headnode operations (create, assign networks, console access)

### Fast Provisioning 
stretch goal: TPM-attested boot loader
