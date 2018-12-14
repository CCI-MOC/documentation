[06/03/2015] - Minying 
## Overview 

Tempest is a set of integration tests that runs against Openstack cluster(s). The main components are API tests, Unit tests, Scenario tests, Stress tests and Third Party API tests

**API tests** are running all the api calls of the environment both positively (expect success) and negatively (expect failure). This is the most basic and important tests of Tempest, because Scenario and Stress tests are calling the same functions as the API tests just in a different manner. 

**Unit tests** are tests that run against the tempest code itself. 
This is for running Tempest against the Harvard production environment.

**Scenario tests** are "throughout path" tests of OpenStack usage. i.e. use cases 

**Stress tests** are use to detect condition bugs which will not be easily found during fictional tests but will be encountered by users in large deployments. User who's running this test must have access to the controller, because the tests look into controller's log file.   

**Third Party API tests** to test non OpenStack native APIs 


## How to run Tempest 

Make sure you have access to the back door and you have proxychains set up correctly 
To run Tempest **Without VPN access** against the production environment. You have to do double ssh tunnel to our back door and run the tests with proxychains. 

    ssh -L 5000:140.247.152.201:22 username@140.247.152.200 
    
open a new window and run

    ssh username@localhost -p 5000 

and then open another new window, go to tempest home directory and run 

    # for all the tests
    proxychains ./run_tempest.sh 

    # for all api tests in nova
    proxychains ./run_tempest.sh tempest.api.nova

    # for a specific tests such as test_rebuild_server test in nova api calls
    proxychains ./run_tempest.sh -d tempest.api.compute.servers.test_server_actions.ServerActionsTestJSON.test_rebuild_server
    
## Expected Results

Unfortunately, there are a number of tests in Tempest that are expected to fail due to the way our production environment is set up or bugs exists in OpenStack (that they unwilling to fix, or fixed in release later than icehouse). 
 
#### API tests
**Keystone [identity]**
  
All pass

**Nova [compute]**

All pass except 
  * `test_allocate_floating_ip_from_nonexistent_pool` 
    * Because floating ip pool here is hard-coded in to satisfy other tests 

**Neutron [network]**
  
All pass except 
  * `test_create_security_group_rule_with_invalid_ports`
    * This test expects Neutron to retun 400 because it's trying to create security group rule with invalid port_range_min=none (). But our environment let the request pass and return a 200 success. 
  * `test_delete_external_networks_with_floating_ip` 
    * This test deletes the external network with floating ip attach to it. The first response is NeutronError: NetworkInUse: Unable to complete operation on network [] There are one or more ports still in use on the network. However Neutron actually behaves correctly (can successfully delete the network) with the error message
    * After the first response(NetworkInUse), Neutron will schedule to delete the port/floating ip that is attached to the network and then delete the network again. This time we will be able to get 204 (successfully delete). But Tempest already got the first response and throw the error exit at this point.  

**Cinder [volume]**

All pass except
  * `test_volume_crud_with_volume_type_and_extra_specs` 
    * We don't have crud

**Sahara [data-processing]**

  All fail, Sahara currently not in the production environment

**Swift [object-storage]**

  Almost all fail, because we are using Ceph, and common header like "content-length" is not available to tempest  

**Heat [orchestration]**

  * `setUpClass(tempest.api.orchestration.stacks.test_swift_resources.SwiftResourcesTestJSON)`
    * we are using Ceph

#### Scenario tests

see https://bugs.launchpad.net/tempest/+bug/1298472

#### Stress tests

I'm still trying to figure this out, their documentation seems to be outdated.....

#### Unit tests
All pass

### Third Party API tests

Ignore 