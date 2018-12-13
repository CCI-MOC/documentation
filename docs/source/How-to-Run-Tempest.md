_Also see: [Run Tempest on NEU BigData Clusters](https://github.com/CCI-MOC/moc/wiki/Run-Tempest-on-NEU-BigData-Clusters)_

### Run Tempest test suite
#### Run all tempest tests:
navigate to the tempest folder in your root directory and run the command 

`./run_tempest.sh`

---

#### Run Service-specific API tests: 
if you want to run API tests on a specific service (Nova, Cinder, ... etc), the syntax is as follows: 

`./run_tempest.sh tempest.api.$service_type$`

where service_type can be compute, volume, image ... etc. You can also:
* specify the API version by adding `.v1` or `.v2` ... etc. 
* add `.admin` after service_type to run admin tests only 

---

#### Run a smaller group of tests:
In order to do this easily, know the tests' directory path. For example, say we want to run Nova tests on servers. We know these tests are located in tempest/api/compute/servers. Then, the command will be:

`./run_tempest.sh tempest.api.compute.servers`

And say we want to run test_instance_actions.py tests from that directory:

 `./run_tempest.sh tempest.api.compute.servers.test_instance_actions`

And so on!

---

#### Run a single test:
In test_instance_actions.py, there are two tests. if we want to run only one of them, we do the following:

`./run_tempest.sh tempest.api.service_type.file_name.class_name.test_method_name`

So to run test_list_instance_actions, which is a test method in test_instance_actions.py:

`./run_tempest.sh tempest.api.compute.servers.test_instance_actions.InstanceActionsTestJSON.test_list_instance_actions`

---

#### Run Scenario tests:
 
`./run_tempest.sh tempest.scenario`

--- 
#### More options!
to get the list from the command line, run `./run_tempest.sh --help`

* -V, --virtual-env        Always use virtualenv.  Install automatically if not present
* -N, --no-virtual-env     Don't use virtualenv.  Run tests in local environment
* -n, --no-site-packages   Isolate the virtualenv from the global Python environment
* -f, --force              Force a clean re-build of the virtual environment. Useful when dependencies have been added.
* -u, --update             Update the virtual environment with any newer package versions
* -s, --smoke              Only run smoke tests
* -t, --serial             Run testr serially
* -C, --config             Config file location
* -h, --help               Print this usage message
* -d, --debug              Run tests with testtools instead of testr. This allows you to use PDB
* -- [TESTROPTIONS]        After the first '--' you can pass arbitrary arguments to testr

---

### Run Tempest unit tests
this has been deprecated, and run_tests.sh will be removed in the new release. 