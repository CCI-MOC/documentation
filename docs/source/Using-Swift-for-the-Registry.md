UP <https://github.com/CCI-MOC/moc-public/wiki/OpenShift>

Reference:

    Overall: <https://access.redhat.com/documentation/en-us/openshift_container_platform/3.5/html/installation_and_configuration/setting-up-the-registry>

    Extended Configuration: <https://access.redhat.com/documentation/en-us/openshift_container_platform/3.5/html/installation_and_configuration/setting-up-the-registry#install-config-registry-extended-configuration

    Swift Specific: <https://docs.docker.com/registry/storage-drivers/swift/>

1) create a registry-config file

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

2) (shouldn't need to do this) delete the registry configuration

        oc delete svc/docker-registry dc/docker-registry

3) Create a default registry

        oadm -n default registry --create=true

4) Create a new secret from the registry-config.yml file (from step 1)

        oc -n default secrets new registry-config config.yml=/home/cloud-user/registry/registry-contfig.yml


5) Add the registry-config secret as a volume to the registry's deployment configuration and mount it at /etc/docker/registry

        oc -n default volume dc/docker-registry --add --type=secret --secret-name=registry-config -m /etc/docker/registry/

7) Updates the registry to reference the configuration path by adding the REGISTRY_CONFIGURATION_PATH environment variable.

        oc -n default env dc/docker-registry REGISTRY_CONFIGURATION_PATH=/etc/docker/registry/config.yml

9) add replicas

        oc -n default edit dc/docker-registry

   make:

        spec: replicas: 2

8) Redeploy the registry

        oc -n default rollout latest docker-registry

9) As the registry is cached - restart the OpenShift service

        systemctl restart atomic-openshift-master-api
        systemctl restart atomic-openshift-master-controllers

10) As a simple confirmation, start a test project.  The registry should appear in Horizon:

    Project tab
       +-> Object Store tab
             
Look for a container with the name specified in the registry-config file under storage.swift.container.  That container is the one being used as the OpenShift Registry.