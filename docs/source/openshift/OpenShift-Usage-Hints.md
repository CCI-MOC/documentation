## OpenShift Usage Hints
This page lists a few hints as to how to do things using open shift.

 1. To run the containers as root (useful for debugging, not useful for running in production):
 Edit the dc/<pod name>
```shell
       spec:containers:securityContext:privileged:true
```
 rollout the pod:
```shell
        oc rollout latest <dc>
```
 or press the deploy option (in the OpenShift GUI).
 2. To run a pod on specific node/nodes, add that/those node/nodes into a new region.
 Edit the dc/<pod name>
```shell
       spec:nodeSelector:region: <region name>      
```
 rollout the pod:
```shell
        oc rollout latest <dc>
```
 or press the deploy option (in the OpenShift GUI).
 