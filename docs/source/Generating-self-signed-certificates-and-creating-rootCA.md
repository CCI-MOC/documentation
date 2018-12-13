**WARNING:-**

**Do not try to generate these certs on foreman node. We faced the issue where the certificates generated using the same command on foreman node were not working fine on the controller/compute nodes even after adding them to the root certificate authority.**
**We generated the certificates on one of the host having RHEL 7.1 installed.**

Create a **certauth.cfg** file containing default values for generating rootCA certificate:-

    [req]
    distinguished_name = req_distinguished_name
    prompt = no
    
    [req_distinguished_name]
    C = US
    ST = MA
    L = Boston
    O = Custom Cert Authority
    OU = Signing
    CN = custom.cert.org

    [v3_req]
    
    [alt_names]


Create **controller.cfg** file with default values for host certificate. This file will also contain the alternate DNS names.

	[req]
	distinguished_name = req_distinguished_name
	x509_extensions = v3_req
	prompt = no

	[req_distinguished_name]
	C = US
	ST = MA
	L = Boston
	O = Mass Open Cloud
	OU = Engineering
	CN = 129.10.3.37

	[v3_req]
	keyUsage = keyEncipherment, dataEncipherment
	extendedKeyUsage = serverAuth
	subjectAltName = @alt_names

	[alt_names]
	DNS.1 = 129.10.3.37
	DNS.2 = 127.0.0.1
	DNS.3 = controller01.massopencloud.org
	DNS.4 = keystone.massopencloud.org
	DNS.5 = nova.massopencloud.org
	DNS.6 = cinder.massopencloud.org
	DNS.7 = glance.massopencloud.org
	DNS.8 = neutron.massopencloud.org
	DNS.9 = ceph.massopencloud.org
	DNS.10 = swift.massopencloud.org
	DNS.11 = ceilometer.massopencloud.org
	DNS.12 = horizon.massopencloud.org

Generating root CA key and Cert:-

    # openssl genrsa -out rootCA.key 2048
    # openssl req -x509 -new -nodes -sha256 -key rootCA.key -days 3650 \
    -out rootCA.crt -config certauth.cfg

Generating host key:-

    # openssl genrsa -out host.key 2048

Generating CSR request for host:-

    # openssl req -new -sha256 -key host.key -out host.csr \
    -config controller.cfg

Signing the CSR request using rootCA:-

    # openssl x509 -req -sha256 -in host.csr -CA rootCA.crt \
    -CAkey rootCA.key -CAcreateserial -out host.crt \
    -days 3650 -extensions v3_req -extfile controller.cfg

From where does python requests module pick up the trusted root certs?

This can be found using the command "python -mrequests.certs".
We need to add our rootCA.crt certificate at the end of this file so that requests module can trust the certificates signed using this rootCA cert.


Right now, we generate only one host.crt certificate. We make different copies of the same certificate with different names (glance.crt, nova.crt, horizon.crt ...). If required, we can generate different certificates for each individual service.


How to install the root certs and add all the ssl certificates:-

The steps to do this have been puppetized. One can have a look at the puppet scripts present at https://github.com/rahulait/kilo-puppet/tree/master/moc_openstack/manifests/ssl .

In general, the steps include:-
* Adding rootCA.crt to file /etc/pki/tls/certs/ca-bundle.crt
* Copying host.crt as horizon.crt, glance.crt, nova.crt ... to location /etc/pki/tls/certs.
* Copying host.key as horizon.key, glance.key, nova.key ... to location /etc/pki/tls/private.

Next, specify the respective key and cert in the config files of different services.


Setting up local DNS:-
* Modify the /etc/hosts file and add the host-ip with DNS names.

    129.10.3.37 controller01.massopencloud.org keystone.massopencloud.org nova.massopencloud.org cinder.massopencloud.org glance.massopencloud.org neutron.massopencloud.org ceph.massopencloud.org swift.massopencloud.org ceilometer.massopencloud.org horizon.massopencloud.org


How to test if its working fine?

Perform these steps on bash shell.

    # python
    >>> 
    >>> import requests
    >>> requests.get("https://controller01.massopencloud.org:5000")
    <Response [300]>
    >>> 

If you get any SSL error related to verify failed, it means that rootCA is not added to trusted root authorities and you need to debug and fix it.


Steps needed to be done when creating a new environment under puppet:-
* Generate the rootCA.crt, host.crt and host.key files.
* Place them in /etc/puppet/environments/**env_name**/modules/moc_openstack/files/certs/ directory.