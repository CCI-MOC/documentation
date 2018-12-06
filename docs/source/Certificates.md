UP <https://github.com/CCI-MOC/moc-public/wiki/OpenShift-Debugging>

1) to see if there are multiple certificates with hawkular or ...

        oc exec -it hawkular-metrics-x1r4j cat /var/run/secrets/kubernetes.io/serviceaccount/ca.crt

    and count the certificates.

