## Tips & Tools
 -  [Foreman-Puppet](../infrastructure/Foreman-Puppet.html)
 -  Credential for Red Hat package subscription: see bitwarden Redhat Packages Subsciption
 -  [Transitioning from Django to AngularJS](Transitioning-from-Django-to-AngularJS.html) 
 -  [Create new vm on eos-ctl01](Create-new-vm-on-eos-ctl01.html)
 -  [How to setup Twiki](How-to-setup-Twiki.html)
 -  **tcpdump**  (Peter Desnoyers)
```shell
# To capture:
  tcpdump -w <file> port 80
# or:
  tcpdump -i <interface> -w <file> port 80

# (the later if there are multiple ethernets and the default one is wrong)
#^C to stop when you're done.

#To read HTTP from the file, easiest is to use strings:
 <strings <file> >
   ....
```

### Useful OpenStack related resources
 -  [Reference blogs and articles](Reference-blogs-and-articles.html)
 -  Disabling defroute:
 In few of the files, we need to update the defroute parameter so that the default route out of the node 
 is through this interface(say in case of public interface)
```shell
BOOTPROTO="dhcp"
DEVICE="enp130s0f0.1053"
HWADDR="90:e2:ba:84:d5:70"
ONBOOT=yes
DEFROUTE=yes
PEERDNS=no
PEERROUTES=no
VLAN=yes
```
 This can be checked using ip route
```shell
ip route
default via 172.16.0.1 dev enp130s0f0.1053 
```
 -  [Programming Tatics](Programming-Tactics.html)
