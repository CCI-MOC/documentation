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

