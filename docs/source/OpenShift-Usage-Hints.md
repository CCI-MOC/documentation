This page lists a few hints as to how to do things using open shift.

1) To run the containers as root (useful for debugging, not useful for running in production):

   Edit the dc/<pod name>

       spec:containers:securityContext:privileged:true

   rollout the pod:

        oc rollout latest <dc>

   or press the deploy option (in the OpenShift GUI).

2) To run a pod on specific node/nodes, add that/those node/nodes into a new region.

   Edit the dc/<pod name>

       spec:nodeSelector:region: <region name>      

   rollout the pod:

        oc rollout latest <dc>

   or press the deploy option (in the OpenShift GUI).

3) Using Host Volumes

    Host directory denotes the host address on which the pod is running, in our case- the **Node**. 
For pods to share data, mount the directory using host directories. 
Also, they should be on the same node to access the volume. You also need elevated privileges for accessing host directories.

        oc volume dc/<dc-name> --add --mount-path=<directory address on the pod> --path==<directory address on the node> 