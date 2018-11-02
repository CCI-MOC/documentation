```
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
 934M 3962G neutron-openvswi-INPUT  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
 934M 3962G nova-api-INPUT  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
 221K  135M ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            multiport dports 8776 /* 001 cinder incoming */
 544M 3460G ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            multiport dports 80,443,3260,3306,5000,35357,5672,8773,8774,8775,8776,8777,9292,6080 /* 001 controller incoming */
 352K  214M ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            multiport dports 8000,8003,8004 /* 001 controller incoming pt2 */
4280K  932M ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            multiport dports 9191,9292 /* 001 glance incoming */
  16M   18G ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            multiport dports 9696 /* 001 neutron server (API) */
   40  2400 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            multiport dports 8386 /* 001 sahara server (API) */
    0     0 ACCEPT     47   --  *      *       0.0.0.0/0            0.0.0.0/0            /* 002 gre */
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            multiport dports 443,5671 /* 002 ssl controller incoming */
  26M 4574M ACCEPT     udp  --  *      *       0.0.0.0/0            0.0.0.0/0            multiport dports 4789 /* 002 vxlan udp */
 343M  479G ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
19040 1622K ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0           
 6565  378K ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0           
  215 12972 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22
 176K   45M REJECT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 neutron-filter-top  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
    0     0 neutron-openvswi-FORWARD  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
    0     0 nova-filter-top  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
    0     0 nova-api-FORWARD  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
    0     0 REJECT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited

Chain OUTPUT (policy ACCEPT 860M packets, 3861G bytes)
 pkts bytes target     prot opt in     out     source               destination         
 860M 3861G neutron-filter-top  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
 860M 3861G neutron-openvswi-OUTPUT  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
 860M 3861G nova-filter-top  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
 860M 3861G nova-api-OUTPUT  all  --  *      *       0.0.0.0/0            0.0.0.0/0           

Chain neutron-filter-top (2 references)
 pkts bytes target     prot opt in     out     source               destination         
 860M 3861G neutron-openvswi-local  all  --  *      *       0.0.0.0/0            0.0.0.0/0           

Chain neutron-openvswi-FORWARD (1 references)
 pkts bytes target     prot opt in     out     source               destination         

Chain neutron-openvswi-INPUT (1 references)
 pkts bytes target     prot opt in     out     source               destination         

Chain neutron-openvswi-OUTPUT (1 references)
 pkts bytes target     prot opt in     out     source               destination         

Chain neutron-openvswi-local (1 references)
 pkts bytes target     prot opt in     out     source               destination         

Chain neutron-openvswi-sg-chain (0 references)
 pkts bytes target     prot opt in     out     source               destination         

Chain neutron-openvswi-sg-fallback (0 references)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0           

Chain nova-api-FORWARD (1 references)
 pkts bytes target     prot opt in     out     source               destination         

Chain nova-api-INPUT (1 references)
 pkts bytes target     prot opt in     out     source               destination         
96468   11M ACCEPT     tcp  --  *      *       0.0.0.0/0            10.31.27.207         tcp dpt:8775

Chain nova-api-OUTPUT (1 references)
 pkts bytes target     prot opt in     out     source               destination         

Chain nova-api-local (1 references)
 pkts bytes target     prot opt in     out     source               destination         

Chain nova-filter-top (2 references)
 pkts bytes target     prot opt in     out     source               destination         
 860M 3861G nova-api-local  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
```