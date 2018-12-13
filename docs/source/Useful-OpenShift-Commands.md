# Useful OpenShift Commands
[UP](OpenShift.html)
* [Pruning (system cleanup)](pruning-(system-cleanup).html)
* [Limits](limits.html)
* [Projects](projects.html)

0) Restarts the master service:
 
        systemctl restart atomic-openshift-master

1) Log in: 

        oc login -u <username>:<password>

2) List the projects: 

        oc projects   

3) Create a project:              

        oadm new-project <project name> --description="<project description>"

4) gets current project:      

        oc project

5) sets current project:      

        oc project <project name>

6) lists pods in current project: 

        oc get pods

7) describe a pod:            

        oc describe pod <a pod name from command 5>

8) cat log from a pod:        

        oc logs <a pod name from command 5>

9) list nodes:   

        oc get nodes

10) delete a node:

        oc delete <node name>

11) Move the project over to a different region - also can be used to determine the region a project is currently running in.

  1) `oc edit dc/[project name]`
      
    a) Either add or edit

        spec:
          template:
              spec:
                  nodeSelector:
                      region: [region name]

  2) Redeploy the project:

        oc -n default rollout latest docker-registry

So far the only time we have had to do this is to move the docker-registry from the default region to the infra region.

---
### Notes

On project creation:
* `oadm new-project` uses the default template
* `oc new-project` and "Create New Project" (from the GUI) will allow the specification of one project template
* Cluster admins can use: `oc process ... | oc create -f ...` (This is still a bit of a research project - haven't used this yet).

[see](https://github.com/openshift/origin/issues/4381)

