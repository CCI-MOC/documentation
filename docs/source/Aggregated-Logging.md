UP<>

reference: <https://docs.openshift.com/container-platform/3.5/install_config/aggregate_logging_sizing.html>

0) disable logging:

        ansible-playbook \
            /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
            -e openshift_logging_install_logging=False

1) Create project:

        oadm new-project logging --node-selector=""

    note the --node-selector="", this will distribute logging across all of the nodes in the cluster.

2) switch to the logging project:

        oc project logging

3) added the following to the /etc/ansible/hosts file:

        #aggregated logging
        openshift_logging_install_logging=True
        openshift_logging_curator_default_days=7
        openshift_logging_kibana_hostname=kibana.apps.osh.massopen.cloud
        openshift_logging_kibana_ops_hostname=kibana-ops.apps.osh.massopen.cloud
        openshift_logging_kibana_replica_count=0
        openshift_logging_es_cluster_size=3
        openshift_logging_namespace=logging
        openshift_logging_es_pvc_prefix=logging-es-

4) Setting up elastic search with privileged account and persistent storage: 

        oc adm policy add-scc-to-user privileged system:serviceaccount:logging:aggregated-logging-elasticsearch

        oc scale dc/logging-es-4o9ou402 --replicas=0
        oc patch dc/logging-es-4o9ou402 -p '{"spec":{"template":{"spec":{"containers":[{\ 
             "name":"elasticsearch","securityContext":{"privileged": true}}]}}}}'

5) run the ansible script:  

        ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml

6) due to a bug in the ansible scripts, logging was failing with this entry in the log files:

        Exception in thread "main" java.lang.RuntimeException: Unable to load index mapping for io.fabric8.elasticsearch.kibana.mapping.empty.  The key was not in the settings or it specified a file that does not exists.

    as per the following commit to fix this issue:

        https://github.com/openshift/openshift-ansible/pull/4657/files       

    Updated the scripts, and added the following line to /usr/share/ansible/openshift-ansible/roles/openshift_logging/templates/elasticsearch.yml.j2 (line 43):

        io.fabric8.elasticsearch.kibana.mapping.empty: /usr/share/elasticsearch/index_patterns/com.redhat.viaq-openshift.index-pattern.json
