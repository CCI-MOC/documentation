UP: <https://github.com/CCI-MOC/moc-public/wiki/OpenShift>

0) modify the /etc/origin/master/master-config.yaml to configure the router subdomain:

        routingConfig:
          subdomain:  "apps.osh.massopen.cloud"

1) In DNS setup wild card subdomains to point to the infra nodes using the external IP addresses.
   use *.apps.osh.massopen.cloud as the wild card subdomain and map one entry to each infra node (where 
   the routers are running.

2) Run the following to generate the self-signed certificates:

        CA=/etc/origin/master
        oadm ca create-server-cert --signer-cert=$CA/ca.crt \
              --signer-key=$CA/ca.key --signer-serial=$CA/ca.serial.txt \
              --hostnames='*.apps.osh.massopen.cloud' \
              --cert=cloudapps.crt --key=cloudapps.key

3) bundle the certs in a way that the router expects them:

        cat cloudapps.crt cloudapps.key $CA/ca.crt > cloudapps.router.pem

4) Start the router:

        oadm router router --replicas=2 --selector='region=infra' \
             --default-cert=cloudapps.router.pem --service-account=router