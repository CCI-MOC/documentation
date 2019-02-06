## Useful OpenShift Commands
[UP](OpenShift.html)
 -  [Pruning (system cleanup)](pruning-system-cleanup.html)
 -  [Limits](limits.html)
 -  [Projects](projects.html)

 1. Restarts the master service:
    ```shell 
        systemctl restart atomic-openshift-master
    ```
 1. Log in: 
    ```shell
        oc login -u <username>:<password>
    ```
 1. List the projects: 
    ```shell
        oc projects   
    ```
 1. Create a project:              
    ```shell
        oadm new-project <project name> --description="<project description>"
    ```
 1. gets current project:      
    ```shell
        oc project
    ```
 1. sets current project:      
    ```shell
        oc project <project name>
    ```
 1. lists pods in current project: 
    ```shell
        oc get pods
    ```
 1. describe a pod:            
    ```shell
        oc describe pod <a pod name from command 5>
    ```
 1. cat log from a pod:        
    ```shell
        oc logs <a pod name from command 5>
    ```
 1. list nodes:   
    ```shell
        oc get nodes
    ```
 1. delete a node:
    ```shell
        oc delete <node name>
    ```
 1. Move the project over to a different region - also can be used to determine 
    the region a project is currently running in.
    ```shell
     oc edit dc/[project name]
    ```
    Either add or edit
    ```shell
        spec:
          template:
              spec:
                  nodeSelector:
                      region: [region name]
    ```
    Redeploy the project:
    ```shell
        oc -n default rollout latest docker-registry
    ```

So far the only time we have had to do this is to move the docker-registry from the default region 
to the infra region.

---

### Notes
On project creation:
 -  `oadm new-project` uses the default template
 -  `oc new-project` and "Create New Project" (from the GUI) will allow the specification of one project template
 -  Cluster admins can use: `oc process ... | oc create -f ...` 
 (This is still a bit of a research project - haven't used this yet).

[see](https://github.com/openshift/origin/issues/4381)
