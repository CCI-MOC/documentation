UP: <https://github.com/CCI-MOC/moc-public/wiki/OpenShift>

Reference: <https://docs.openshift.com/container-platform/3.5/install_config/cluster_metrics.html#metrics-namespace>

1) Run the ansible script for either with PV or without PV:

    With persistent volume:
    
        ansible-playbook <OPENSHIFT_ANSIBLE_DIR>/common/openshift-cluster/openshift_metrics.yml \
           -e openshift_metrics_install_metrics=True \
           -e openshift_metrics_hawkular_hostname=hawkular-metrics.example.com \
           -e openshift_metrics_cassandra_storage_type=pv

    ---
    Without persistent volume: 

        ansible-playbook <OPENSHIFT_ANSIBLE_DIR>/common/openshift-cluster/openshift_metrics.yml \
           -e openshift_metrics_install_metrics=True \
           -e openshift_metrics_hawkular_hostname=hawkular-metrics.example.com

2) Not sure if this is needed, to add the hawkular OpenShift Agent - this gets deployed to the default project
    a) Get the 2 configuration files

        wget https://github.com/openshift/origin-metrics/blob/enterprise/hawkular-openshift-agent/hawkular-openshift-agent-configmap.yaml
        wget https://github.com/openshift/origin-metrics/blob/enterprise/hawkular-openshift-agent/hawkular-openshift-agent.yaml

    b) To deploy the agent:

        oc create -f hawkular-openshift-agent-configmap.yaml -n default
        oc process -f hawkular-openshift-agent.yaml | oc create -n default -f -
        oc adm policy add-cluster-role-to-user hawkular-openshift-agent system:serviceaccount:default:hawkular-openshift-agent