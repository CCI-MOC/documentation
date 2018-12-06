UP: <https://github.com/CCI-MOC/moc-public/wiki/OpenShift>

Reference: <https://docs.openshift.com/container-platform/3.5/install_config/aggregate_logging.html>

1) create a new project for aggregated logging:

        oadm new-project logging --node-selector=""
        oc project logging

2)