This document describes the strucutre of our ceph cluster as of May 12, 2021.

## Ceph

The ceph cluster used for our openstack and baremetal environments is made up of 10 OSD servers and 3 monitors.

Each OSD server has 10 HDDs and 1 nVme drive. The HDDs make up the "size" root, while the nVme drives make the "performance" root.

Everything is triple replicated except RGW storage pools.

## How do I login?

1. Login to the monitors over the foreman network (172.16.0.0/19)

This works if you have access to the MOC VPN or some gateway.

| Host | IP Address |
| ---- | ---------- |
| kzn-mon1 | 172.16.19.15 |
| kzn-mon2 | 172.16.17.14 |
| kzn-mon3 | 172.16.5.14 |

From the monitors you can run various ceph commands. Look into /etc/hosts to SSH to the osd servers and RGWs.

2. Login to the public RGW that we use for OpenStack Swift.

| Host | IP Address |
| ---- | ---------- |
| kzn-rgw1.infra.massopen.cloud | 128.31.24.16 |
| kzn-rgw2.infra.massopen.cloud | 128.31.24.17 |
| kzn-swift.infra.massopen.cloud | 128.31.24.18 |

The swift endpoint will take you to 1 of the gateways.

From there look into the hosts file and then you can get to the monitors.

## Server Configuration

All our OSD servers are Dell PowerEdge R730xd. Here's the configuration of an OSD server.

| Field              | Value    |
| ------------------ | ------ |
| CPU(s) | 40 |
| Thread(s) per core | 2 |
| Core(s) per socket |10 |
| Socket(s) | 2 |
| Model Name | Intel(R) Xeon(R) CPU E5-2660 v3 @ 2.60GHz |
| RAM | 128 GB DDR3 |
| Model | PowerEdge R730xd |
| Networking | Dual 10GB Nics |
| Storage | 10 X 10TB 7.2k rpm drives for "size" root |
| Storage | 1 x  8TB PCIe nVme drive for "performance" root|
| Storage | 2 X 128GB SSDs for OS |

We have 3 Dell PowerEdge R330. Each server acts as a monitor (mon), manager (mgr) and metadata (mds) server. Here's the configuration of those servers:

| Field              | Value    |
| ------------------ | ------ |
| CPU(s) | 4 |
| Thread(s) per core | 1 |
| Core(s) per socket | 4 |
| Socket(s) | 1 |
| Model Name | Intel(R) Xeon(R) CPU E3-1220 v5 @ 3.00GHz |
| RAM | 64 GB DDR3 |
| Model | PowerEdge R330 |
| Networking | Dual 10GB Nics |
| Storage | 2 X 128GB SSDs for OS |


The Rados Gateways are virtual machines on our RHEV/ovirt cluster.

The test iSCSI server is a VM on the BMI intel node.

Here's the output of ceph status which shows various ceph services.

```
[root@kzn-mon1 ~]# ceph -s
  cluster:
    id:     ed141a90-e501-481c-90c2-b9e24a7c9b54
    health: HEALTH_OK

  services:
    mon:         3 daemons, quorum kzn-mon1,kzn-mon2,kzn-mon3 (age 6d)
    mgr:         kzn-mon1(active, since 6d), standbys: kzn-mon2, kzn-mon3
    mds:         cephfs:1 {0=kzn-mon1=up:active} 2 up:standby
    osd:         130 osds: 129 up (since 5d), 129 in (since 8d)
    rgw:         4 daemons active (kzn-rgw-j-01, kzn-rgw-j-02, kzn-rgw1, kzn-rgw2)
    tcmu-runner: 1 daemon active (kzn-vbmi01stack.kzn.moc:m2/ceph-iscsi-1)

  task status:
    scrub status:
        mds.kzn-mon1: idle

  data:
    pools:   22 pools, 2000 pgs
    objects: 50.08M objects, 76 TiB
    usage:   223 TiB used, 910 TiB / 1.1 PiB avail
    pgs:     1995 active+clean
             5    active+clean+scrubbing+deep

  io:
    client:   43 MiB/s rd, 417 MiB/s wr, 781 op/s rd, 3.12k op/s wr
```

## OSD tree

Here's the output of `ceph osd tree`.

```
[root@kzn-mon1 ~]# ceph osd tree
ID    CLASS WEIGHT     TYPE NAME               STATUS REWEIGHT PRI-AFF
-1002         72.99988 root performance
  -35          7.29999     host kzn-osd05-nvme
  272   ssd    7.29999         osd.272             up  1.00000 1.00000
  -37          7.29999     host kzn-osd06-nvme
  273   ssd    7.29999         osd.273             up  1.00000 1.00000
  -33          7.29999     host kzn-osd07-nvme
  274   ssd    7.29999         osd.274             up  1.00000 1.00000
  -39          7.29999     host kzn-osd08-nvme
  275   ssd    7.29999         osd.275             up  1.00000 1.00000
  -41          7.29999     host kzn-osd09-nvme
  276   ssd    7.29999         osd.276             up  1.00000 1.00000
  -43          7.29999     host kzn-osd10-nvme
  277   ssd    7.29999         osd.277             up  1.00000 1.00000
  -45          7.29999     host kzn-osd11-nvme
  278   ssd    7.29999         osd.278             up  1.00000 1.00000
  -47          7.29999     host kzn-osd12-nvme
  279   ssd    7.29999         osd.279             up  1.00000 1.00000
  -49          7.29999     host kzn-osd13-nvme
  280   ssd    7.29999         osd.280             up  1.00000 1.00000
  -51          7.29999     host kzn-osd14-nvme
  281   ssd    7.29999         osd.281             up  1.00000 1.00000
-1001       1069.16003 root size
   -6        106.91600     host kzn-osd05
  152   hdd    8.90999         osd.152             up  1.00000 1.00000
  153   hdd    8.90999         osd.153             up  1.00000 1.00000
  154   hdd    8.90999         osd.154             up  1.00000 1.00000
  155   hdd    8.90999         osd.155             up  1.00000 1.00000
  156   hdd    8.90999         osd.156             up  1.00000 1.00000
  157   hdd    8.90999         osd.157             up  1.00000 1.00000
  158   hdd    8.90999         osd.158             up  1.00000 1.00000
  159   hdd    8.90999         osd.159             up  1.00000 1.00000
  160   hdd    8.90999         osd.160             up  1.00000 1.00000
  161   hdd    8.90999         osd.161             up  1.00000 1.00000
  162   hdd    8.90999         osd.162             up  1.00000 1.00000
  163   hdd    8.90999         osd.163             up  1.00000 1.00000
   -7        106.91600     host kzn-osd06
  164   hdd    8.90999         osd.164             up  1.00000 1.00000
  165   hdd    8.90999         osd.165             up  1.00000 1.00000
  166   hdd    8.90999         osd.166             up  1.00000 1.00000
  167   hdd    8.90999         osd.167             up  1.00000 1.00000
  168   hdd    8.90999         osd.168             up  1.00000 1.00000
  169   hdd    8.90999         osd.169             up  1.00000 1.00000
  170   hdd    8.90999         osd.170             up  1.00000 1.00000
  171   hdd    8.90999         osd.171             up  1.00000 1.00000
  172   hdd    8.90999         osd.172             up  1.00000 1.00000
  173   hdd    8.90999         osd.173             up  1.00000 1.00000
  174   hdd    8.90999         osd.174             up  1.00000 1.00000
  175   hdd    8.90999         osd.175             up  1.00000 1.00000
   -8        106.91600     host kzn-osd07
  176   hdd    8.90999         osd.176             up  1.00000 1.00000
  177   hdd    8.90999         osd.177             up  1.00000 1.00000
  178   hdd    8.90999         osd.178             up  1.00000 1.00000
  179   hdd    8.90999         osd.179             up  1.00000 1.00000
  180   hdd    8.90999         osd.180             up  1.00000 1.00000
  181   hdd    8.90999         osd.181             up  1.00000 1.00000
  182   hdd    8.90999         osd.182             up  1.00000 1.00000
  183   hdd    8.90999         osd.183             up  1.00000 1.00000
  184   hdd    8.90999         osd.184             up  1.00000 1.00000
  185   hdd    8.90999         osd.185             up  1.00000 1.00000
  186   hdd    8.90999         osd.186             up  1.00000 1.00000
  187   hdd    8.90999         osd.187             up  1.00000 1.00000
   -9        106.91600     host kzn-osd08
  188   hdd    8.90999         osd.188             up  1.00000 1.00000
  189   hdd    8.90999         osd.189             up  1.00000 1.00000
  190   hdd    8.90999         osd.190             up  1.00000 1.00000
  191   hdd    8.90999         osd.191             up  1.00000 1.00000
  192   hdd    8.90999         osd.192             up  1.00000 1.00000
  193   hdd    8.90999         osd.193             up  1.00000 1.00000
  194   hdd    8.90999         osd.194             up  1.00000 1.00000
  195   hdd    8.90999         osd.195             up  1.00000 1.00000
  196   hdd    8.90999         osd.196             up  1.00000 1.00000
  197   hdd    8.90999         osd.197             up  1.00000 1.00000
  198   hdd    8.90999         osd.198             up  1.00000 1.00000
  199   hdd    8.90999         osd.199             up  1.00000 1.00000
  -21        106.91600     host kzn-osd09
  200   hdd    8.90999         osd.200             up  1.00000 1.00000
  201   hdd    8.90999         osd.201             up  1.00000 1.00000
  202   hdd    8.90999         osd.202             up  1.00000 1.00000
  203   hdd    8.90999         osd.203             up  1.00000 1.00000
  204   hdd    8.90999         osd.204             up  1.00000 1.00000
  205   hdd    8.90999         osd.205             up  1.00000 1.00000
  206   hdd    8.90999         osd.206             up  1.00000 1.00000
  207   hdd    8.90999         osd.207             up  1.00000 1.00000
  208   hdd    8.90999         osd.208             up  1.00000 1.00000
  209   hdd    8.90999         osd.209             up  1.00000 1.00000
  210   hdd    8.90999         osd.210             up  1.00000 1.00000
  211   hdd    8.90999         osd.211             up  1.00000 1.00000
  -23        106.91600     host kzn-osd10
  212   hdd    8.90999         osd.212             up  1.00000 1.00000
  213   hdd    8.90999         osd.213             up  1.00000 1.00000
  214   hdd    8.90999         osd.214             up  1.00000 1.00000
  215   hdd    8.90999         osd.215             up  1.00000 1.00000
  216   hdd    8.90999         osd.216             up  1.00000 1.00000
  217   hdd    8.90999         osd.217             up  1.00000 1.00000
  218   hdd    8.90999         osd.218             up  1.00000 1.00000
  219   hdd    8.90999         osd.219             up  1.00000 1.00000
  220   hdd    8.90999         osd.220             up  1.00000 1.00000
  221   hdd    8.90999         osd.221             up  1.00000 1.00000
  222   hdd    8.90999         osd.222             up  1.00000 1.00000
  223   hdd    8.90999         osd.223             up  1.00000 1.00000
  -25        106.91600     host kzn-osd11
  224   hdd    8.90999         osd.224             up  1.00000 1.00000
  225   hdd    8.90999         osd.225             up  1.00000 1.00000
  226   hdd    8.90999         osd.226             up  1.00000 1.00000
  227   hdd    8.90999         osd.227             up  1.00000 1.00000
  228   hdd    8.90999         osd.228             up  1.00000 1.00000
  229   hdd    8.90999         osd.229             up  1.00000 1.00000
  230   hdd    8.90999         osd.230             up  1.00000 1.00000
  231   hdd    8.90999         osd.231             up  1.00000 1.00000
  232   hdd    8.90999         osd.232             up  1.00000 1.00000
  233   hdd    8.90999         osd.233             up  1.00000 1.00000
  234   hdd    8.90999         osd.234             up  1.00000 1.00000
  235   hdd    8.90999         osd.235             up  1.00000 1.00000
  -27        106.91600     host kzn-osd12
  236   hdd    8.90999         osd.236             up  1.00000 1.00000
  237   hdd    8.90999         osd.237             up  1.00000 1.00000
  238   hdd    8.90999         osd.238             up  1.00000 1.00000
  239   hdd    8.90999         osd.239             up  1.00000 1.00000
  240   hdd    8.90999         osd.240             up  1.00000 1.00000
  241   hdd    8.90999         osd.241             up  1.00000 1.00000
  242   hdd    8.90999         osd.242             up  1.00000 1.00000
  243   hdd    8.90999         osd.243             up  1.00000 1.00000
  244   hdd    8.90999         osd.244             up  1.00000 1.00000
  245   hdd    8.90999         osd.245             up  1.00000 1.00000
  246   hdd    8.90999         osd.246             up  1.00000 1.00000
  247   hdd    8.90999         osd.247             up  1.00000 1.00000
  -29        106.91600     host kzn-osd13
  248   hdd    8.90999         osd.248             up  1.00000 1.00000
  249   hdd    8.90999         osd.249             up  1.00000 1.00000
  250   hdd    8.90999         osd.250             up  1.00000 1.00000
  251   hdd    8.90999         osd.251             up  1.00000 1.00000
  252   hdd    8.90999         osd.252             up  1.00000 1.00000
  253   hdd    8.90999         osd.253           down        0 1.00000
  254   hdd    8.90999         osd.254             up  1.00000 1.00000
  255   hdd    8.90999         osd.255             up  1.00000 1.00000
  256   hdd    8.90999         osd.256             up  1.00000 1.00000
  257   hdd    8.90999         osd.257             up  1.00000 1.00000
  258   hdd    8.90999         osd.258             up  1.00000 1.00000
  259   hdd    8.90999         osd.259             up  1.00000 1.00000
  -31        106.91600     host kzn-osd14
  260   hdd    8.90999         osd.260             up  1.00000 1.00000
  261   hdd    8.90999         osd.261             up  1.00000 1.00000
  262   hdd    8.90999         osd.262             up  1.00000 1.00000
  263   hdd    8.90999         osd.263             up  1.00000 1.00000
  264   hdd    8.90999         osd.264             up  1.00000 1.00000
  265   hdd    8.90999         osd.265             up  1.00000 1.00000
  266   hdd    8.90999         osd.266             up  1.00000 1.00000
  267   hdd    8.90999         osd.267             up  1.00000 1.00000
  268   hdd    8.90999         osd.268             up  1.00000 1.00000
  269   hdd    8.90999         osd.269             up  1.00000 1.00000
  270   hdd    8.90999         osd.270             up  1.00000 1.00000
  271   hdd    8.90999         osd.271             up  1.00000 1.00000
   -1                0 root default
```

The hosts are from `kzn-osd05` to `kzn-osd14`.

Hosts `kzn-osd01` to `kzn-osd04` were the fujitsu servers that used to host the performance root, those machines are now decommisioned/repurposed.

You'll notice that for every `kzn-osdX` host we have a corresponding `kzn-osdX-nvme` this was done to trick ceph into letting us
put the nVme drives in a different root even though they are in the same physical host.

This configuration flag must be set to false, otherwise ceph will move the nvmes to their respective hosts.

```
[root@kzn-mon1 ~]# grep "osd crush update on start" /etc/ceph/ceph.conf
osd crush update on start = false
```

## Pools

Here are the current pools we have. This will, of course, change over time.

```
[root@kzn-mon1 ~]# ceph df
RAW STORAGE:
    CLASS     SIZE        AVAIL       USED        RAW USED     %RAW USED
    hdd       1.0 PiB     861 TiB     199 TiB      199 TiB         18.78
    ssd        73 TiB      48 TiB      24 TiB       24 TiB         33.39
    TOTAL     1.1 PiB     910 TiB     223 TiB      223 TiB         19.72

POOLS:
    POOL                    ID      STORED      OBJECTS     USED        %USED     MAX AVAIL
    .rgw.root                72     1.1 KiB           7     1.1 MiB         0       239 TiB
    rbd                      92      45 GiB      11.71k     135 GiB      0.02       239 TiB
    .rgw                    112     541 KiB       2.39k     448 MiB         0       239 TiB
    .rgw.gc                 113     173 KiB          32      13 MiB         0       239 TiB
    .users.uid              114     226 KiB         278      36 MiB         0       239 TiB
    .usage                  115      65 MiB          32      65 MiB         0       239 TiB
    .rgw.buckets.index      116      45 MiB       1.21k      45 MiB         0       239 TiB
    .rgw.control            119         0 B           8         0 B         0       239 TiB
    .rgw.buckets            133     6.7 TiB       1.94M      10 TiB      1.43       477 TiB
    .log                    139        28 B         177     192 KiB         0       239 TiB
    .users                  141       121 B           3     576 KiB         0       239 TiB
    .rgw.buckets.extra      143     132 KiB           5     1.1 MiB         0       239 TiB
    m2                      144     6.6 TiB       1.74M      20 TiB      2.70       239 TiB
    ostack2                 145      42 TiB      40.56M     129 TiB     15.30       239 TiB
    performance2            146     8.1 TiB       2.12M      24 TiB     37.15        14 TiB
    cephfs                  149      13 TiB       3.72M      39 TiB      5.16       239 TiB
    cephfsmeta              150     257 MiB         158     771 MiB         0       239 TiB
    openshift4              153     532 MiB         194     1.6 GiB         0       239 TiB
    default.rgw.meta        159         0 B           0         0 B         0       239 TiB
    default.rgw.control     160         0 B           0         0 B         0       239 TiB
    default.rgw.log         161         0 B           0         0 B         0       239 TiB
    .users.email            168         0 B           0         0 B         0        32 TiB

```

I have enabled the pg autoscaler so it automatillcay manages the number of placement groups for each pool.

```
[root@kzn-mon1 ~]# ceph osd pool autoscale-status
POOL                  SIZE TARGET SIZE          RATE RAW CAPACITY  RATIO TARGET RATIO EFFECTIVE RATIO BIAS PG_NUM NEW PG_NUM AUTOSCALE
.users.email            0              1.28571426868       74520G 0.0000                               1.0     16            on
.users.uid          225.8k                       3.0        1060T 0.0000                               1.0     16            on
.rgw                541.0k                       3.0        1060T 0.0000                               1.0     32            on
.rgw.buckets         6837G                       1.5        1060T 0.0094                               1.0     64            on
openshift4          532.3M                       3.0        1060T 0.0000                               1.0     64            on
.rgw.gc             173.1k                       3.0        1060T 0.0000                               1.0     16            on
performance2         8271G                       3.0       74520G 0.3330                               1.0    256            on
default.rgw.meta        0                        3.0        1060T 0.0000                               1.0     16            on
.log                   28                        3.0        1060T 0.0000                               1.0     16            on
m2                   6778G                       3.0        1060T 0.0187                               1.0    128            on
default.rgw.log         0                        3.0        1060T 0.0000                               1.0     16            on
.rgw.buckets.extra  132.4k                       3.0        1060T 0.0000                               1.0     32            on
.rgw.buckets.index  46307k                       3.0        1060T 0.0000                               1.0     32            on
.rgw.root            1090                        3.0        1060T 0.0000                               1.0     16            on
rbd                 46038M                       3.0        1060T 0.0001                               1.0     32            on
.users                121                        3.0        1060T 0.0000                               1.0     16            on
.rgw.control            0                        3.0        1060T 0.0000                               1.0     16            on
default.rgw.control     0                        3.0        1060T 0.0000                               1.0     16            on
.usage              66180k                       3.0        1060T 0.0000                               1.0     32            on
ostack2             42525G                       3.0        1060T 0.1175                               1.0   1024            on
cephfsmeta          256.9M                       3.0        1060T 0.0000                               1.0     16            on
cephfs              13303G                       3.0        1060T 0.0368                               1.0    128            on
```

## Rados Gateways

We have 4 RGWs, setup in pairs of 2.

The first pair, `kzn-rgw1` and `kzn-rgw2`, are used for OpenStack Swift and as such are configured to work with keystone. These have public IP addresses.

The second pair, `kzn-rgw-j-01` and `kzn-rgw-j-02`, are used by the baremetal OpenShift 4.X cluster with the OCS operator. These hosts do not have a public IP since it's not required.

Both pairs of RGWs have a virtual IP that is managed by `corosync`, `pcs`, and `pacemaker` for high availibilty.
