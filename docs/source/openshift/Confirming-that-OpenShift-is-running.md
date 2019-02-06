## Confirming that OpenShift is Running
 1. Are the nodes running:
    ```shell
        oc get nodes
    ```
 1. Are the hostsubnets up:
    ```shell
        >oc get hostsubnets

        NAME      HOST      HOST IP     SUBNET
        m-1       m-1       10.1.0.15   10.128.1.0/24
        m-2       m-2       10.1.0.17   10.128.0.0/24
        n-1       n-1       10.1.0.10   10.128.4.0/24
        n-2       n-2       10.1.0.19   10.128.2.0/24
        n-3       n-3       10.1.0.6    10.128.3.0/24
    ```
 1. In project default:
    ```shell
        oc -n default get nodes
    ```
    Should list:
    ```shell
        >oc get pods
        NAME                       READY     STATUS    RESTARTS   AGE
        docker-registry-1-xv1m9    1/1       Running   0          1h
        registry-console-1-9p5gc   1/1       Running   0          1h
        router-1-deploy            0/1       Error     0          1h
    ```
    Sometimes the registry or router don't deploy, to redeploy the router:
    ```shell
        oc -n default rollout latest router
    ```
    After redeploying the router:
    ```shell
        >oc get pods
        NAME                       READY     STATUS    RESTARTS   AGE
        docker-registry-1-xv1m9    1/1       Running   0          1h
        registry-console-1-9p5gc   1/1       Running   0          1h
        router-1-deploy            0/1       Error     0          1h
        router-2-56d1h             0/1       Pending   0          4m
        router-2-deploy            1/1       Running   0          4m
        router-2-s012f             1/1       Running   0          4m
        router-2-sb1cb             1/1       Running   0          4m
        router-2-wj8hm             0/1       Pending   0          4m
    ```
    To redeploy the registry:
    ```shell
        oc -n default rollout latest docker-registry
    ```
