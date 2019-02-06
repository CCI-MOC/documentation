## OpenShift Limits
[UP](Useful-OpenShift-Commands.html)

create the limit range
```shell
    oc create -f <limit range yml file from 1> -n <project>
```
view limits:
```shell
    oc get limits -n <project>
```
describe the limit ranges:
```shell
    oc describe limit resource-limits -n <project>
```
delete limits
```shell
    oc delete limit <limit name>
```
