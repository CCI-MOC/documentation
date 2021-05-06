# TrueNAS NFS Storage for Zero OpenShift Cluster

## Overview

We repurposed a recently retired Fujitsu server, `10.0.17.8`, to
use as temporary storage to relieve storage capacity issues on the
[Zero cluster][].

[zero cluster]: https://console-openshift-console.apps.zero.massopen.cloud/

### Connecting to the system

You can reach the system by first logging into
kzn-rtr.infra.massopen.cloud (or any of the OpenShift nodes).

You can access the web UI at <http://10.253.0.10/>, or by setting up
port forwarding from your local machine, e.g:

```
ssh -L 8080:10.253.0.10:80 kzn-rtr.infra.massopen.cloud
```

And then connecting to <http://localhost:8080>.

You can log into the system using `ssh` if your ssh key has been added
to `root`'s `authorized_keys` file, or you can connect as the `csi`
user using the private key embedded in the OpenShift cluster:

```
oc -n democratic-csi get secret moc-nfs-democratic-csi-driver-config \
  -o yaml |
  yq -r '.data."driver-config-file.yaml"' |
  base64 -d |
  yq -r .sshConnection.privateKey
```

## Operating System

The server runs [TrueNAS][] (neè [FreeNAS][]) 12, which is based on FreeBSD.

[TrueNAS]: https://www.truenas.com/
[FreeNAS]: https://www.freenas.org/

## Networking

One of the system's two 10Gb interfaces is attached to VLAN 210:

```
# ifconfig ix0
ix0: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
        description: ix0
        options=a538b9<RXCSUM,VLAN_MTU,VLAN_HWTAGGING,JUMBO_MTU,VLAN_HWCSUM,WOL_UCAST,WOL_MCAST,WOL_MAGIC,VLAN_HWFILTER,VLAN_HWTSO,RXCSUM_IPV6>
        ether 90:1b:0e:0f:5d:8a
        inet 10.253.0.10 netmask 0xfffffe00 broadcast 10.253.1.255
        media: Ethernet autoselect (10Gbase-Twinax <full-duplex,rxpause,txpause>)
        status: active
        nd6 options=9<PERFORMNUD,IFDISABLED>
```

## Zpool configuration

The system has 39 838GB drives.

The drives are configured as a set of four RAIDZ2 vdevs with two hot
spares.

```
  pool: pool0
 state: ONLINE
config:

        NAME                                            STATE     READ WRITE CKSUM
        pool0                                           ONLINE       0     0     0
          raidz2-0                                      ONLINE       0     0     0
            gptid/f463ba10-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f4e63ba8-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f5b71bd9-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f5a8c04f-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f6761b6c-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f6962c80-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f703659f-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f741c71e-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f83c5c41-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
          raidz2-1                                      ONLINE       0     0     0
            gptid/f78c5ea8-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f7cdae80-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f8828b60-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f9028771-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f9c16102-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/f9f71362-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fa36b199-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fab63c1a-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fb2fa199-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
          raidz2-2                                      ONLINE       0     0     0
            gptid/fbac1ab9-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fc134b95-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fc85d895-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fcf9e00b-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fd5f5654-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fdad80f7-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fdee72e7-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fe2ab932-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/fe594a0e-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
          raidz2-3                                      ONLINE       0     0     0
            gptid/fea1d2f2-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ff15ad27-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ff1ece3f-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ff4a32f6-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ff6b07ae-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ff9e36b0-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ffb8521a-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/ffcb2138-adb7-11eb-b057-901b0e42a41b  ONLINE       0     0     0
            gptid/000b03bb-adb8-11eb-b057-901b0e42a41b  ONLINE       0     0     0
        spares
          gptid/001d8210-adb8-11eb-b057-901b0e42a41b    AVAIL
          gptid/0024cda9-adb8-11eb-b057-901b0e42a41b    AVAIL
```

## dnsmasq configuration

The NFS server runs `dnsmasq` in order to provide address to clients
on VLAN 210. TrueNAS doesn't provide DHCP services out of the box, so
it was necessary to manually configured the service.

We largely followed the model described in "[DNSMasq in a FreeNAS Jail
-- A better approach][dnsmasq-freenas]".

[dnsmasq-freenas]: https://blog.udance.com.au/2020/03/25/dnsmasq-in-a-freenas-jail-a-better-approach/

`dnsmasq` runs in a jail at 10.253.0.11, with a configuration file
located outside the jail at `/mnt/pool0/moc/config/dnsmasq.conf`. The
configuration can be found in
<https://github.com/larsks/truenas-dnsmasq-config>.
