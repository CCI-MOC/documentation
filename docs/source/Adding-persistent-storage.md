UP <https://github.com/CCI-MOC/moc-public/wiki/OpenShift>

Ref:
1) More General <https://docs.openshift.com/container-platform/3.5/install_config/persistent_storage/index.html>
   For setting open stack user/passord/... <https://docs.openshift.com/container-platform/3.5/install_config/configuring_openstack.html#install-config-configuring-openstack>

2) Using Cinder <https://docs.openshift.com/container-platform/3.5/install_config/persistent_storage/persistent_storage_cinder.html#install-config-persistent-storage-persistent-storage-cinder>


1. Create storageclass (that is set as the default storageclass) for cinder

        vi cinder-storageclass.yaml

        kind: StorageClass
        apiVersion: storage.k8s.io/v1beta1
        metadata:
          name: cinder-storageclass
          annotations:
            storageclass.beta.kubernetes.io/is-default-class: "true"
        provisioner: kubernetes.io/cinder
        parameters:
          type: Ceph
          availability: nova


    note: parameters:type: needs to match the type that is displayed in the volumes in horizon.

2. Create the storageclass from that file: 

        oc create -f cinder-storageclass.yaml

  Note: cannot edit via:

        oc edit storageclass cinder-storageclass

      have to delete and then reload for changes to take place:

        oc delete storageclass cinder-storageclass
        ...
 
6. On each node (including the master) - probably not needed.

        systemctl stop atomic-openshift-node
        ovs-ofctl del-flows br0 -O OpenFlow13
        systemctl start atomic-openshift-node