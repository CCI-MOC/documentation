# Pruning Setup
[UP](OpenShift.html)

References:
* [Pruning Images](https://docs.openshift.com/container-platform/3.5/admin_guide/pruning_resources.html#pruning-images)
* [Pruning Builds](https://docs.openshift.com/container-platform/3.5/admin_guide/pruning_resources.html#pruning-builds)
* [Pruning Deployments](https://docs.openshift.com/container-platform/3.5/admin_guide/pruning_resources.html#pruning-deployments)

OpenShift has a project that will schedule containers to run at specified intervals, so in the future, this can be done completely within OpenShift.

Created an sa account with the name image-xxxxxxxx-xxxx that has the cluster privilege of system:pruner.


        oc create -n default -f - <<EOF
        apiVersion: v1
        kind: ServiceAccount
        metadata:
          name: pruner
        EOF

        oadm policy add-cluster-role-to-user system:image-pruner -n default -z pruner

        oc get -n default sa/pruner --template='{{range .secrets}}{{printf "%s\n" .name}}{{end}}'

        oc get -n default secrets pruner-xxxx-xxxx --template='{{.data.token}}' | base64 -d

Get token from last tap and replace the xxxxx with the token.

For now, the simplest way to do this is using cron to schedule some pruning jobs.


cron on m-1

        0 0-23/2 * * *  oadm prune builds --orphans --keep-complete=5 --keep-failed=1 --keep-younger-than=60m --confirm
        0 1-23/2 * * *  oadm prune deployments --orphans --keep-complete=5 --keep-failed=1 --keep-younger-than=60m --confirm
        0 0-23/2 30 * *  oc login --token=xxxxx ; oadm prune images --keep-tag-revisions=3 --keep-younger-than=60m --confirm
        0 1-23/2 30 * *  oc login --token=xxxxx ; oadm prune images --prune-over-size-limit --confirm

cron on m-2

        0 1-23/2 * * *  oadm prune builds --orphans --keep-complete=5 --keep-failed=1 --keep-younger-than=60m --confirm
        0 0-23/2 * * *  oadm prune deployments --orphans --keep-complete=5 --keep-failed=1 --keep-younger-than=60m --confirm
        0 1-23/2 30 * *  oc login --token=xxxxx && oadm prune images --keep-tag-revisions=3 --keep-younger-than=60m
        0 0-23/2 30 * *  oc login --token=xxxxx && oadm prune images --prune-over-size-limit --confirm

