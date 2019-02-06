## Using Swift for the Registry
[UP](OpenShift.html)

Reference:
 -  [Overall](https://access.redhat.com/documentation/en-us/openshift_container_platform/3.5/html/installation_and_configuration/setting-up-the-registry)
 -  [Extended Configuration](https://access.redhat.com/documentation/en-us/openshift_container_platform/3.5/html/installation_and_configuration/setting-up-the-registry#install-config-registry-extended-configuration)
 -  [Swift Specific](https://docs.docker.com/registry/storage-drivers/swift/)

 1. Create a registry-config file
    ```yaml
        # registry-config.yml
        version: 0.1
        log:
          level: debug
        http:
          addr: :5000
        storage:
          cache:
            blobdescriptor: inmemory
          delete:
            enabled: true
          swift:
            username: [account]
            password: [password]
            authurl: [Keystone auth URL including the /v3]
            authversion: 3
            tenantid: [Project ID for OpenShift]
            domainid: [domainid for Project for OpenShift]
            insecureskipverify: true
            region: [OpenStack Region for project for OpenShift]
            container: [Any Name for the Swift Container]
        auth:
          openshift:
            realm: openshift
        middleware:
          registry:
            - name: openshift
          repository:
            - name: openshift
              options:
                acceptschema2: false
                pullthrough: true
                enforcequota: false
                projectcachettl: 1m
                blobrepositorycachettl: 10m
          storage:
            - name: openshift
    ```
 1. Delete the registry configuration *(shouldn't need to do this)*
    ```shell
        oc delete svc/docker-registry dc/docker-registry
    ```
 1. Create a default registry
    ```shell	
        oadm -n default registry --create=true
    ```
 1. Create a new secret from the registry-config.yml file (from step 1)
    ```shell
        oc -n default secrets new registry-config config.yml=/home/cloud-user/registry/registry-contfig.yml
    ```
 1. Add the registry-config secret as a volume to the registry's deployment configuration 
    and mount it at `/etc/docker/registry`
    ```shell
        oc -n default volume dc/docker-registry --add --type=secret --secret-name=registry-config -m /etc/docker/registry/
    ```
 1. Updates the registry to reference the configuration path by adding 
    the `REGISTRY_CONFIGURATION_PATH` environment variable.
    ```shell
        oc -n default env dc/docker-registry REGISTRY_CONFIGURATION_PATH=/etc/docker/registry/config.yml
    ```
 1. Add replicas
    ```shell
        oc -n default edit dc/docker-registry
    ```
    make:
    ```shell
        spec: replicas: 2
    ```
 1. Redeploy the registry
    ```shell
        oc -n default rollout latest docker-registry
    ```
 1. As the registry is cached - restart the OpenShift service
    ```shell
        systemctl restart atomic-openshift-master-api
        systemctl restart atomic-openshift-master-controllers
    ```
 1. As a simple confirmation, start a test project.  The registry should appear in Horizon:
    ```shell
        Project tab
            +-> Object Store tab
    ```     

Look for a container with the name specified in the registry-config file under storage.swift.container.

That container is the one being used as the OpenShift Registry.
