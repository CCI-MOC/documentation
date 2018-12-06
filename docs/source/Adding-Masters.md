UP: <https://github.com/CCI-MOC/moc-public/wiki/Adding-a-Node>

Reference: <https://docs.openshift.com/container-platform/3.5/install_config/adding_hosts_to_existing_cluster.html#adding-nodes-advanced>

Adding masters:

(Advanced install method)  

This cannot be used to change the masters to the HA (5 node configuration) form.

Reference: <https://docs.openshift.com/container-platform/3.5/install_config/adding_hosts_to_existing_cluster.html#adding-nodes-advanced>

To add new masters:

  On the new master: 
  
      follow steps 0-13 here <https://github.com/CCI-MOC/moc-public/wiki/OpenShift-Service-Setup-on-Engage1>

  Master:

  1) This will update all of the sensible playbooks:

        yum update atmoic-openshift-utils

  2) Edit /etc/ansible/hosts 
     A) add 'new_masters' and 'new_nodes' to the [OSEv3:children] 
     B) Add a new section called [new_nodes] to the 
     C) Add the new nodes under the [new_nodes] section

     File should look like:

        [OSEv3:children]
        masters
        nodes
        new_masters
        new_nodes

        [masters]
        smaster001

        [new_masters]
        smaster002
        smaster003

        [nodes]
        smaster001 openshift_node_lables="{'region':'infra', 'zone':'default'}" open shift_schedulable=false
        snode001 openshift_node_lables="{'region':'infra', 'zone':'default'}"
        snode002 openshift_node_lables="{'region':'infra', 'zone':'default'}"
        snode003 openshift_node_lables="{'region':'infra', 'zone':'default'}"
        snode004 openshift_node_lables="{'region':'default', 'zone':'default'}"
        
        [new_nodes]
        smaster002 openshift_node_lables="{'region':'infra', 'zone':'default'}" open shift_schedulable=false
        smaster003 openshift_node_lables="{'region':'infra', 'zone':'default'}" open shift_schedulable=false

  3) run the following ansible playbook:
  
        ansible-playbook [-i /path/to/hostsFile] \
            /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-master/scaleup.yml