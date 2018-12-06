UP: <https://github.com/CCI-MOC/moc-public/wiki/OpenShift>

0)  Goal, install OpenShift on Engage1 with the following features:

     High Availability
     Multi-tenant networking
     Using Keystone Authentication
     multiple docker registries using swift
     dynamically allocated persistent volumes using cinder
     Pruning of image/deployments/builds
     Monitoring
     cluster resource contraints
     default project contraints
     define roles in open shift and map them to keystone
     cluster topology 

     Instructions can be found at (https://docs.openshift.com/container-platform/3.5/install_config/install/quick_install.html)

1) A shared network must exist between master and node hosts

   --> this may limit a general instance to one project, and instances for private data to individual projects.

   --> The OpenStack subnetwork is set to 10.0.0.0/20 (IPv4) GatewayIP 10.0.0.1

   --> The OpenShift service subnetwork is set to 172.30.0.0/16

2) 2 sets of VMs were created as other people also need this:

        Name        internal ip external ip  VCPU RAM(GB)  node domain  volume(GB)
        master-1    10.0.0.6    128.31.22.70    8      16        infra         300
        master-2    10.0.0.5    128.31.22.69    8      16        infra         300
        etcd-1      10.0.0.14   128.31.22.63    2       4                       10
        etcd-2      10.0.0.12   128.31.22.74    2       4                       10
        etcd-3      10.0.0.15   128.31.22.15    2       4                       10
        node-1      10.0.0.16   128.31.22.38    2       4        infra          80
        node-2      10.0.0.9    128.31.22.11    2       4        infra          80
        node-3      10.0.0.13   128.31.22.37    2       4      default          80

        m-1         10.1.0.15   128.31.22.40    8      16        infra         300
        m-2         10.1.0.17   128.31.22.68    8      16        infra         300
        e-1         10.1.0.11   128.31.22.81    2       4                       10
        e-2         10.1.0.18   128.31.22.89    2       4                       10
        e-3         10.1.0.20   128.31.22.73    2       4                       10
        n-1         10.1.0.10   128.31.22.76    2       4        infra          80
        n-2         10.1.0.19   128.31.22.75    2       4        infra          80
        n-3         10.1.0.6    128.31.22.82    2       4      default          80

   These were registered in a subdomain using an external DNS server.

   By default, SELinix is enabled - to check:
     view: /etc/selinux/config

        SELINUX = enforcing
        SELINUXTYPE=targeted

3) on every VM, run the following perl script:

        #!/usr/perl
        
        #read the pool id from the subscription
        my $pool_id=0;
        if( open(FP,"subscription-manager list --available --matches '*Red Hat OpenShift Container Platform, Standard*' |"))
            {
            while(my $line=<FP>)
                {
                print "line $line";
                if($line=~ /^Pool ID:[ \t]+([0-9a-fA-F]+)/)
                    {
                    print "***> found pool_id";
                    $pool_id=$1;
                    }
                }
            }
        if($pool_id==0) 
            {
            print "Error: pool_id not found, check the subscription-manager list";
            die();
            }
        
        system('subscription-manager attach --pool='.$pool_id);
        system('yum -y install yum-utils');
        system('subscription-manager repos --disable="*"');
        system('yum-config-manager --disable \*');
        system('subscription-manager repos '
                .' --enable="rhel-7-server-rpms"'
                .' --enable="rhel-7-server-extras-rpms"'
                .' --enable="rhel-7-server-ose-3.5-rpms"'
                .' --enable="rhel-7-fast-datapath-rpms"');
        system('yum -y install wget git net-tools bind-utils iptables-services bridge-utils'
                 .' bash-completion gcc python-virtualenv atomic-openshift-utils'
                 .' atomic-openshift-excluder atomic-openshift-docker-excluder');
        system('yum -y update');
        system('atomic-openshift-excluder unexclude');
        system(' yum -y install docker');
        
        # add insecure-registry to the /etc/sysconfig/docker file
        if( open(my $fh, '<', "/etc/sysconfig/docker") )
            {
            my @lines=<$fh>;
            close($fh);
            open($fh, '>', "/etc/sysconfig/docker");
            foreach $l (@lines)
                {
                # find  OPTIONS='--selinux-enabled --insecure-registry 172.30.0.0/16'
                if($l=~/^OPTIONS[ \t]*/ && !($l=~/insecure-registry/))
                    {
                    $l=~/[^']+'[^']+/;
                    print $fh $&." --insecure-registry 172.30.0.0/16'\n";
                    print $&." --insecure-registry 172.30.0.0/16'\n";
                    }
                else
                    {
                    print $fh $l;
                    print $l;
                    }
                }
            close($fh);
            }
        else
            {
            print "Error cannot find /etc/sysconfig/docker";
            die();
            }

        # Do the simple stupid thing with /etc/sysconfig/docker-storage-setup
        if( open( my $fh, '>', "/etc/sysconfig/docker-storage-setup"))
            {
            print $fh "DEVS=vdb\n";
            print $fh "VG=docker-vg\n";
            close($fh);
            }
        system('docker-storage-setup');

        #Add rados and erics public keys to the authorized hosts files
        my $robs_key= 'Robs public key';
        my $rados_key='Rados public key';
        my $erics_key='Erics public key';

        if( open( my $fh, '>', "/root/.ssh/authorized_keys"))
            {
            print $fh $robs_key."\n";
            print $fh $rados_key."\n";
            print $fh $erics_key."\n";
            close($fh);
            }
        
        if( open( my $fh, '>', "/home/cloud-user/.ssh/authorized_keys"))
            {
            print $fh $robs_key."\n";
            print $fh $rados_key."\n";
            print $fh $erics_key."\n";
            close($fh);
            }


    Note: setup storage for containers
    options:
        Option A) Use an additional block device
        Option B) Use an existing, specified volume group
        Option C) Use the remaining free space from the volume group where your root file is located

    For either A or B (uses the volumes created in step 3), On each system:

        vi /etc/sysconfig/docker
        
        The file only needs these 2 lines:

            DEVS=vdb
            VG=docker-vg

        Note: the OpenShift and Redhat documentation will have DEVS=/dev/vdc, however the mount 
              point is /dev/vdb and the docker-storage-setup script will give an error saying 
              '/dev//dev/vdb does not exist', so just use vdb.

        run:
     
            docker-storage-setup
  
13) Ensure host Access

    use ssh key forwarding, and from the host ensure access to each node just using the short name (m-1, m-2, e-1, e-2, e-3, n-1, n-2, n-3).


14) Follow the Advanced Install for OpenShift

    On the master edit /etc/ansible/hosts file
    
        vi /etc/ansible/hosts

    File contents:

        [OSEv3:children]
        masters
        etcd
        nodes
        
        # Set variables common for all OSEv3 hosts
        [OSEv3:vars]
        ansible_ssh_user=root
        
        #use keystone as the identity provider
        openshift_master_identity_providers=[{'name': 'keystone_auth', 'login': 'true', 'challenge': 'true', 'url': '[OpenStack Keystone URL]', 'domainName': 'default', 'kind': 'KeystonePasswordIdentityProvider'}]

        # use OpenStack as the cloud provider
        # Openstack
        openshift_cloudprovider_kind=openstack
        openshift_cloudprovider_openstack_auth_url=[OpenStack Keystone URL]
        openshift_cloudprovider_openstack_username=[OpenStack Username]
        openshift_cloudprovider_openstack_password=[OpenStack Password]
        openshift_cloudprovider_openstack_domain_name=[OpenStack Domain Name]
        openshift_cloudprovider_openstack_tenant_name=[OpenStack Project Name]
        openshift_cloudprovider_openstack_region=[OpenStack Region Name]

        openshift_master_default_subdomain=osh.massopen.cloud

        openshift_master_cluster_method=native

        debug_level=6
        deployment_type=openshift-enterprise
        # containerized=false

        # start with this on, disable after the cluster is up - we are using an outside DNS server and resolv.conf is managed by openstack - should this be false?
        # openshift_use_dnsmasq=false

        # here are the network settings
        os_sdn_network_plugin_name=redhat/openshift-ovs-multitenant
        osm_cluster_network_cidr=10.128.0.0/14
        osm_host_subnet_length=8
        # defines the Service Network CIDR
        openshift_portal_net=172.30.0.0/16

        [masters]
        m-1 openshift_hostname=m-1 openshift_public_hostname=128.31.22.40 openshift_public_ip=128.31.22.40 containerized=false
        m-2 openshift_hostname=m-2 openshift_public_hostname=128.31.22.68 openshift_public_ip=128.31.22.68 containerized=false

        [etcd]
        e-1 openshift_hostname=e-1 openshift_public_hostname=128.31.22.81 openshift_public_ip=128.31.22.81
        e-2 openshift_hostname=e-2 openshift_public_hostname=128.31.22.89 openshift_public_ip=128.31.22.89
        e-3 openshift_hostname=e-3 openshift_public_hostname=128.31.22.73 openshift_public_ip=128.31.22.73

        [nodes]
        m-1 openshift_hostname=m-1 openshift_public_hostname=128.31.22.40 openshift_public_ip=128.31.22.40 openshift_node_labels="{'region':'infra','zone':'default'}" openshift_schedulable=false
        m-2 openshift_hostname=m-2 openshift_public_hostname=128.31.22.68 openshift_public_ip=128.31.22.68 openshift_node_labels="{'region':'infra','zone':'default'}" openshift_schedulable=false
        n-1 openshift_hostname=n-1 openshift_public_hostname=128.31.22.76 openshift_public_ip=128.31.22.76 openshift_node_labels="{'region':'infra','zone':'default'}"
        n-2 openshift_hostname=n-2 openshift_public_hostname=128.31.22.75 openshift_public_ip=128.31.22.75 openshift_node_labels="{'region':'infra','zone':'default'}"
        n-3 openshift_hostname=n-3 openshift_public_hostname=128.31.22.82 openshift_public_ip=128.31.22.82 openshift_node_labels="{'region':'default','zone':'default'}"


    run:

        ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/config.yml    

    At this point we have achieved the following:

         High Availability
         Multi-tenant networking
         Keystone Authentication
         Native Cloud Support (about 1/2 to persistent volumes).

    Notes: 

        1) there are several values that need to be changed when the virtual machines are restarted

        2) Will need clean virtual machines after every run as the ansible scripts don't clean up after
           themselves.  It is faster to reinitialize the VMs.

        3) Generally need to have 2 or three Infra nodes - all other nodes can be region 'Default'

        4) The master node should not be schedulable.

        5) Nodes can be reassigned a region with 'oc label node shiftnode1.moclocal region=infra --overwrite=true'

        6) Before deleting a virtual machine, run: 

                subscription-manager unregister

            To unsubscribe it from RH.