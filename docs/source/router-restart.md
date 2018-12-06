UP: <https://github.com/CCI-MOC/moc-public/wiki/OpenShift>

If the router didn't come up cleanly.

1) delete the router information in the following order

        [root@m-1 quotas]# oc get dc
        NAME               REVISION   DESIRED   CURRENT   TRIGGERED BY
        docker-registry    4          2         2         config
        registry-console   1          1         1         config
        router             1          2         0         config
        
        [root@m-1 quotas]# oc delete dc/router
        deploymentconfig "router" deleted
        
        [root@m-1 quotas]# oc get serviceaccounts
        NAME       SECRETS   AGE
        builder    2         7d
        default    3         7d
        deployer   2         7d
        registry   3         7d
        router     2         11m
        
        [root@m-1 quotas]# oc delete serviceaccounts/router
        serviceaccount "router" deleted
        
        [root@m-1 quotas]# oc get svc
        NAME               CLUSTER-IP      EXTERNAL-IP   PORT(S)                   AGE
        docker-registry    172.30.106.92   <none>        5000/TCP                  7d
        kubernetes         172.30.0.1      <none>        443/TCP,53/UDP,53/TCP     7d
        registry-console   172.30.36.56    <none>        9000/TCP                  7d
        router             172.30.71.15    <none>        80/TCP,443/TCP,1936/TCP   11m

        [root@m-1 quotas]# oc delete svc/router
        service "router" deleted

        [root@m-1 quotas]# oc get secrets
        NAME                       TYPE                                  DATA      AGE
        builder-dockercfg-5gpkg    kubernetes.io/dockercfg               1         7d
        builder-token-z2z91        kubernetes.io/service-account-token   4         7d
        builder-token-zgr7s        kubernetes.io/service-account-token   4         7d
        default-dockercfg-jp7r1    kubernetes.io/dockercfg               1         7d
        default-token-bj2s5        kubernetes.io/service-account-token   4         7d
        default-token-kxjfc        kubernetes.io/service-account-token   4         7d
        deployer-dockercfg-4kcgw   kubernetes.io/dockercfg               1         7d
        deployer-token-54pd8       kubernetes.io/service-account-token   4         7d
        deployer-token-q78cq       kubernetes.io/service-account-token   4         7d
        registry-certificates      Opaque                                2         7d
        registry-config            Opaque                                1         7d
        registry-dockercfg-kglpc   kubernetes.io/dockercfg               1         7d
        registry-token-3gds6       kubernetes.io/service-account-token   4         7d
        registry-token-dn25r       kubernetes.io/service-account-token   4         7d
        router-certs               kubernetes.io/tls                     2         11m

        [root@m-1 quotas]# oc delete secrets/router-certs
        secret "router-certs" deleted

2) Create the new router + service account ...

        [root@m-1 quotas]# oadm router router --replicas=2 --selector='region=infra' --service-account=router
        info: password for stats user admin has been set to mq1rXhOZ4D
        --> Creating router router ...
            serviceaccount "router" created
            warning: clusterrolebinding "router-router-role" already exists
            deploymentconfig "router" created
            service "router" created
        --> Success
