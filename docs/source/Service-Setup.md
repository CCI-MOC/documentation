    1) Core Usage -> I am just using the default.
        --> can set the env var GOMAXPROCS to 1 to just use 1 core.

    2) https://docs.openshift.com/enterprise/3.0/install_config/install/prerequisites.html (Security Warning).  certain docker opertions are done using root
        so this needs to be limited to admins perhaps contained within a project.

    3) DNS  *.cloudapps.example.com. 300 IN A 192.168.133.2  (need to modify this)

    4) A shared network must exist between master and node hosts
        --> this may limit a general instance to one project, and instances for private data to individual projects.

    5) Ensure SELinix is enabled:   ---> this seems to be the default for RHEL7

        ----- /etc/selinux/config   ------
        SELINUX = enforcing
        SELINUXTYPE=targeted

    6) *** need to register with RHSM  ***
        subscription-manager list --available --matches '*OpenShift*'
        subscription-manager attach --pool=8a85f98156bddb7e0156be94a84d091a
        subscription-manager repos --disable="*"
        yum-config-manager --disable \*
        subscription-manager repos --enable="rhel-7-server-rpms"\
                 --enable="rhel-7-server-extras-rpms"\
                 --enable="rhel-7-server-ose-3.0-rpms"\
                 --enable="rhel-7-fast-datapath-rpms"
    7) install the following sets of packages:
        yum install wget git net-tools bind-utils iptables-services bridge-utils bash-completion
        yum install gcc python-virtualenv
        yum update
        yum install atomic-openshift-utils
        yum install atomic-openshift-excluder atomic-openshift-docker-excluder

    8 Install Docker
        yum install docker

    9 Add --insecure-registry [subnet] to the options parameter
        -------  /etc/sysconfig/docker   -----
        ...
        OPTIONS='--selinux-enabled --insecure-registry 172.30.0.0/16'
        ...
        ----
        vi /etc/sysconfig/docker

        note: this would be nice to add but causes an error when docker is restarted.
            also add the --log-opt as in:

            OPTIONS='--selinux-enabled --log-driver=journald --signature-verification=false --insecure-registry 172.30.0.0/16 --log-opt max-size=5M --log-opt max-file=7'

            --log-opt max-size=???  sets the maximum size of the log
            --log-opt max-file=???  sets the maximum time to keep the file in days

            vi /etc/sysconfig/docker

    10 setup storage for containers
