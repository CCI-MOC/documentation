## Adding Limits
[UP](OpenShift.html)

On each master do the following:
 1. Edited the `master-config.yaml`
    ```yaml
        kubernetesMasterConfig:
          admissionConfig:
            pluginConfig:
              ClusterResourceOverride:   
                configuration:
                  apiVersion: v1
                  kind: ClusterResourceOverrideConfig
                  memoryRequestToLimitPercent: 100  
                  cpuRequestToLimitPercent: 100 
                  #limitCPUToMemoryPercent: 200
    ```
 1. Restarted the master node
    ```shell
        systemctl restart atomic-openshift-master-api
        systemctl restart atomic-openshift-master-controllers
    ```

On each node, do the following:
 1. Edit `the node-config.yaml`
    ```yaml
        kubeletArguments:
          cpu-cfs-quota:
            - "false"
    ```
 1. Restart the node
    ```shell
        systemctl restart atomic-openshift-node
    ```

[Limits for Persistent Storage](Limits-for-Persistent-Storage.html)
