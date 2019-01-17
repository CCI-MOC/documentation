# OpenShift Limits
[UP](Useful-OpenShift-Commands.html)

create the limit range

    oc create -f <limit range yml file from 1> -n <project>

view limits:

    oc get limits -n <project>

describe the limit ranges:

    oc describe limit resource-limits -n <project>

delete limits

    oc delete limit <limit name>

