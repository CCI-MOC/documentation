## Overview

This is a basic instruction for running tempest on NEU BigData Env (129.10.3.30). 

This will likely be overwritten in the future for running tempest on Sensu node.  

## How To

Since the configuration is already done. You should be able to run it just with the command. 

You will see a tempest folder on under `/root`, cd into that folder

For running unit tests = the test for tempest itself
```
./run_tests.sh
```

For only running API tests (i.e. no scenario, no stress) for separate services (because we don't want a super long log file)

```
./run_tempest.sh tempest.api.identity > logs/api_keystone_all.log 2>&1 &
./run_tempest.sh tempest.api.compute > logs/api_nova_all.log 2>&1 &
./run_tempest.sh tempest.api.network > logs/api_neutron_all.log 2>&1 &
./run_tempest.sh tempest.api.image > logs/api_glance_all.log 2>&1 &
./run_tempest.sh tempest.api.volume > logs/api_volume_all.log 2>&1 &
```

To run a smaller set of tests or even a single test, we follow the same format but with more information. For example:

```
tempest.api.volume.admin.test_volume_types.VolumeTypesV2Test.test_volume_crud_with_volume_type_and_extra_specs
```

is to run the specific test `test_volume_crud_with_volume_type_and_extra_specs` in cinder admin api calls. 

The way this works is, you have to find the location of the test, because it's python, you can just call the test with its "path", using `.` as a `/` in bash. In our example. The test is located in `~/tempest/tempest/api/volume/admin/test_volume_types.py` in class `VolumeTypesV2Test` function `test_volume_crud_with_volume_type_and_extra_specs`. 

Note that tempest tests are run with `testr` and they can run in parallel so feel free to type all of them in at once. 

The log files will show up under `/root/tempest/logs`, every run will overwrite the last log file if you don't change the filename the logs writes into.
