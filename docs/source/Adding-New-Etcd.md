1) run the os_p.pl script
   don't worry about the docker stuff in that script - it will fail on that

2) Install etcd but don't start it on the new etcd (e004)

        yum install etcd

3) Add iptable rules on the new etcd

        systemctl enable iptables.service --now

        iptables -N OS_FIREWALL_ALLOW
        iptables -t filter -I INPUT -j OS_FIREWALL_ALLOW
        iptables -A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 2379 -j ACCEPT
        iptables -A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 2380 -j ACCEPT

   save the iptables (RHEL 7)

        yum install iptables-services  # should already be installed
        service iptables save

4) login in to a member of the etcd cluster.  And generate the new etcd server certificates there

        ssh e001
        e001# cd /etc/etcd
        e001# export NEW_ETCD="e004.osh.massopen.cloud"
        e001# host $NEW_ETCD

        e004.osh.massopen.cloud has address 10.0.0.11

        e001# export SAN="IP:10.0.0.11"
        e001# export PREFIX="./generated_certs/etcd-$CN/"
        e001# mkdir ${PREFIX}

    Create the server.csr and server.crt

        e001# openssl req -new -keyout ${PREFIX}server.key \
        -config ca/openssl.cnf \
        -out ${PREFIX}server.csr \
        -reqexts etcd_v3_req -batch -nodes \
        -subj /CN=$CN

        e001# openssl ca -name etcd_ca -config ca/openssl.cnf \
        -out ${PREFIX}server.crt \
        -in ${PREFIX}server.csr \
        -extensions etcd_v3_ca_server -batch

    Create the peer.csr and peer.crt

        e001# openssl req -new -keyout ${PREFIX}peer.key \
        -config ca/openssl.cnf \
        -out ${PREFIX}peer.csr \
        -reqexts etcd_v3_req -batch -nodes \
        -subj /CN=$CN

        e001# openssl ca -name etcd_ca -config ca/openssl.cnf \
        -out ${PREFIX}peer.crt \
        -in ${PREFIX}peer.csr \
        -extensions etcd_v3_ca_peer -batch


        e001# cp ca.crt ${PREFIX}
        e001# cp etcd.conf ${PREFIX}
        e001# tar -czvf ${PREFIX}${CN}.tgz ${PREFIX}

        [root@e001 ~]# etcdctl -C https://e001:2379 --ca-file=/etc/etcd/ca.crt --cert-file=/etc/etcd/peer.crt --key-file=/etc/etcd/peer.key member add e004 https://e004:2380
        Added member named e004 with ID aa4354f760dbf237 to cluster

        ETCD_NAME="e004"
        ETCD_INITIAL_CLUSTER="e001=https://10.0.0.13:2380,e002=https://10.0.0.24:2380,
        e003=https://10.0.0.23:2380,e004=https://e004:2380"
        ETCD_INITIAL_CLUSTER_STATE="existing"