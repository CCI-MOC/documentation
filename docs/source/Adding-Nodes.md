UP: <https://github.com/CCI-MOC/moc-public/wiki/Adding-a-Node>

Reference: <https://docs.openshift.com/container-platform/3.5/install_config/adding_hosts_to_existing_cluster.html#adding-nodes-advanced>

Adding nodes:

(Advanced install method)

Reference: <https://docs.openshift.com/container-platform/3.5/install_config/adding_hosts_to_existing_cluster.html#adding-nodes-advanced>

To add new nodes:

  On the new Node: 
  
      follow steps 0-13 here <https://github.com/CCI-MOC/moc-public/wiki/OpenShift-Service-Setup-on-Engage1>

  Master:

  1) This will update all of the ansible playbooks:

        yum update atomic-openshift-utils

  2) Edit /etc/ansible/hosts 
     A) add 'new_nodes' to the [OSEv3:children] 
     B) Add a new section called [new_nodes] to the 
     C) Add the new nodes under the [new_nodes] section

     File should look like:

            [OSEv3:children]
            masters
            nodes
            new_nodes
            
            [masters]
            master-001
            
            [nodes]
            master-001 openshift_node_lables="{'region':'infra', 'zone':'default'}" open shift_schedulable=false
            node-001 openshift_node_lables="{'region':'infra', 'zone':'default'}"
            node-002 openshift_node_lables="{'region':'infra', 'zone':'default'}"
            node-003 openshift_node_lables="{'region':'infra', 'zone':'default'}"
            node-004 openshift_node_lables="{'region':'default', 'zone':'default'}"
        
            [new_nodes]
            node-005 openshift_node_lables="{'region':'default', 'zone':'default'}"
            node-006 openshift_node_lables="{'region':'default', 'zone':'default'}"
            node-007 openshift_node_lables="{'region':'default', 'zone':'default'}"
            node-008 openshift_node_lables="{'region':'default', 'zone':'default'}"

  3) for each of the nodes, ensure that the node network plugin matches the master network plugins:

        vi /etc/origin/master/master-config.yaml
        vi /etc/origin/node/node-config.yaml

     For the MOC, we are running the multitentant plugin, so everything should match
     networkConfig:networkPluginName: "rehat/openshift-ovs-multitenant"

  3) run the following sensible playbook:
  
        ansible-playbook [-i /path/to/hostsFile] \
            /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-node/scaleup.yml