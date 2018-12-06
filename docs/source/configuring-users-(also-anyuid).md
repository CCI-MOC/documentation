up:<https://github.com/CCI-MOC/moc-public/wiki/OpenShift>

*  To add users to a role to a project:

        oadm policy add-role-to-user <role> <user name>

   example

        oc project <project name>
        oc get users      #list the users
        oadm policy add-role-to-user admin <user name from oc get users>

*  To create an anyuid user
   ref: <https://blog.openshift.com/understanding-service-accounts-sccs/>

        oc create serviceaccount useroot
        oc adm policy add-scc-to-user anyuid -z useroot

   To use:

        oc patch dc/myAppNeedsRoot --patch '{"spec":{"template":{"spec":{"serviceAccountName": "useroot"}}}}'

   or you can do 
  
        oc edit dc/myAppNeedsRoot

   To edit directly.