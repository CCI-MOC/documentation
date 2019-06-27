## Securing Routes
[UP](OpenShift.html)

We have installed the openshift-acme client (https://github.com/tnozicka/openshift-acme) so that anyone can secure a route to their project with it's own certificate from Let's Encrypt.  Once done, the openshift-acme client with renew the certificate when it expires.

The easiest way to go from an unsecured route to a secured on is to:

   1) go to the routes section in the OpenShift console
   2) Edit the route and check the secure box and save.
   3) Edit the yaml of the route adding the 'kubernetes.io/tls-acme: "true" in the metadata: annotations: section as in:
      ```
        metadata:
          annotations:
            kubernetes.io/tls-acme: "true"
      ```

