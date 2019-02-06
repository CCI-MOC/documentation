## OpenShift Spark
**Oshinko** - just manages how many masters/slaves a spark cluster has.  
Can be done through the OpenShift UI - all oshinko does is allow it to be done through the web through a route to the project.

 1. create a service account and add role
```shell
$ oc create sa oshinko
$ oc policy add-role-to-user edit -z oshinko
```
 1. create the app vi yams:
```shell
$ oc new-app -f https://raw.githubusercontent.com/radanalyticsio/oshinko-webui/master/tools/ui-template.yaml
```
