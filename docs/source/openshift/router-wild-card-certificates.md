## Router Wildcard Certificates
[UP](OpenShift.html)

 1. Modify the `/etc/origin/master/master-config.yaml` to configure the router subdomain:
```yaml
        routingConfig:
          subdomain:  "apps.osh.massopen.cloud"
```
 1. In DNS setup wild card subdomains to point to the infra nodes using the external IP addresses.
   use `*.apps.osh.massopen.cloud` as the wild card subdomain and map one entry to each infra node (where 
   the routers are running.
 1. Run the following to generate the self-signed certificates:
```shell
        CA=/etc/origin/master
        oadm ca create-server-cert --signer-cert=$CA/ca.crt \
              --signer-key=$CA/ca.key --signer-serial=$CA/ca.serial.txt \
              --hostnames='*.apps.osh.massopen.cloud' \
              --cert=cloudapps.crt --key=cloudapps.key
```
 1. Bundle the certs in a way that the router expects them:
```shell
        cat cloudapps.crt cloudapps.key $CA/ca.crt > cloudapps.router.pem
```
 1. Start the router:
```shell
        oadm router router --replicas=2 --selector='region=infra' \
             --default-cert=cloudapps.router.pem --service-account=router
```
