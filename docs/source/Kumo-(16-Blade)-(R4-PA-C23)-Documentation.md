<!--Documentation Spreadsheet: [R4-PA-C23 rack layout and networking](inventory_xlsx/R4-PA-C23.xlsx)-->

######10.10.10.0/24: VLAN 3040 
This network is shared with Engage1, see also [[Engage1 Network Documentation]]
For this deployment, server addresses should all be in the range 10.10.10.{200-254}
     
     10.10.10.3    Mangement Switch (Cisco Catalyst 3650)
     10.10.10.4    10G TOR Switch (Cisco Nexus 3548)
     
     For servers, we have used addresses starting from 10.10.10.201:

     10.10.10.201    Supermicro 1    Emergency Box
     10.10.10.202    Supermicro 2    Services Box
     10.10.10.203    Dell R720 Storage Server
     ...
     10.10.10.210    Dell M1000e CMC
     10.10.10.{211-226}    IDRAC for Dell Blades 1-16
  
### Cisco Nexus 3548 Switch
    
    username: admin
    password: P@ssword1

### Cisco Catalyst 3650 Switch
<!--
[Catalyst 3650 Hardware Install Guide](manuals/cat3650_hardware_install_guide.pdf)  
[Catalyst 3650 Getting Started Guide](manuals/cat3650_getting_started_guide.pdf)
-->
     username: admin
     ssh/telnet password: cisco
     enable password: wittinglypreconfigure   
 
     The enable password is what you enter after you log in and type 'enable'.


If the switch is reset to defaults, you must enable aaa-newmodel for SSH to work:
     switch# config t
     switch(config)# aaa new-model 

<!--see [this documentation](http://www.cisco.com/c/en/us/support/docs/security-vpn/secure-shell-ssh/4145-ssh.html) for details.-->

### Dell M1000e Chassis 

<!--
[Chassis Owner's Manual](manuals/dell-poweredge-m1000e_owners-manual-en-us.pdf)     
[M1000e Tech Guidebook](manuals/dell-poweredge-m1000e-tech-guidebook.pdf)
-->

Command to reset something to default ([source](http://en.community.dell.com/techcenter/blades/f/4436/t/19417682)): 
    
    racadm racresetcfg [-m ] where module is [ chassis, kvm, server-n where n= 1 - 16]

