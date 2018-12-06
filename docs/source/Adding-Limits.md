UP: <https://github.com/CCI-MOC/moc-public/wiki/OpenShift>


//// commitment override ////
On each master do the following:

1) edited the master-config.yaml

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

2) restarted the master node

        systemctl restart atomic-openshift-master-api
        systemctl restart atomic-openshift-master-controllers

On each node, do the following:

1) edit the node-config.yaml

        kubeletArguments:
          cpu-cfs-quota:
            - "false"

2) restart the node

        systemctl restart atomic-openshift-node