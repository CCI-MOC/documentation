UP: <https://github.com/CCI-MOC/moc-public/wiki/OpenShift>
Ref: <>

1) Create a VM running the cloud forms image.

2) 1) add a service account in openshift
    oadm new-project management-infra --description="Management Infrastructure"

    oc create -n management-infra -f - <<EOF
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: management-admin
    EOF

    oc create -f - <<EOF
    apiVersion: v1
    kind: ClusterRole
    metadata:
      name: management-infra-admin
    rules:
    - resources:
      - pods/proxy
      verbs:
      - '*'
    EOF

    oadm policy add-role-to-user -n management-infra admin -z management-admin
    oadm policy add-role-to-user -n management-infra management-infra-admin -z management-admin
    oadm policy add-cluster-role-to-user cluster-reader -z management-admin
    oadm policy add-scc-to-user privileged system:serviceaccount:management-infra:management-admin

2) get the token for the service account:

        oc get -n management-infra sa/management-admin --template='{{range .secrets}}{{printf "%s\n" .name}}{{end}}'

    This returns:

        management-admin-dockercfg-xxxxxx
        management-admin-token-xxxxxx

    Use the management-admin-token-*

        oc get -n management-infra secrets management-admin-token-xxxxxx --template='{{.data.token}}' | base64 -d

    This returns the token.

3) Install/enable metrics using defaults and persistent storage:

    ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
       -e openshift_metrics_install_metrics=True \
       -e openshift_metrics_hawkular_hostname=hawkular-metrics.apps.osh.massopen.cloud \
       -e openshift_metrics_cassandra_storage_type=pv

4) Deploy agents to the nodes:

   a) gather 2 config files:

        wget https://raw.githubusercontent.com/openshift/origin-metrics/enterprise/hawkular-openshift-agent/hawkular-openshift-agent-configmap.yaml
        wget https://raw.githubusercontent.com/openshift/origin-metrics/enterprise/hawkular-openshift-agent/hawkular-openshift-agent.yaml

   b) add to default project
        oc create -f hawkular-openshift-agent-configmap.yaml -n default
        oc create -f hawkular-openshift-agent.yaml -n default

5) Deploy the heapster endpoint (the router on the master)
oadm router management-metrics \
-n default \
--service-account=router --ports='443:5000' \
--selector='kubernetes.io/hostname=<master>' \
--stats-port=1937 \
--host-network=false
