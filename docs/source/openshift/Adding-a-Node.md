## Adding a Node
[UP](OpenShift.html)

[reference](https://docs.openshift.com/container-platform/3.5/install_config/adding_hosts_to_existing_cluster.html#adding-nodes-advanced)

 1. Either:
     -  [Adding Masters](Adding-Masters.html)
     -  [Adding Nodes](Adding-Nodes.html)
 1. Confirm all nodes were added:
```shell
        oc get nodes
```
 1. If yes, merge `[new_masters]` section with `[masters]` and delete the `[new_masters]` 
 and merge `[new_nodes]` section with `[nodes]` and delete the `[new_nodes]` section.
