## Benchmarking OpenStack with Rally
Rally is an simple and useful tool for benchmarking Openstack. 
This tutorial will guide you through setting up Rally as well as the basics of using it. 
This tutorial works in a VM running Devstack. 

More information can be found at [https://rally.readthedocs.io/](https://rally.readthedocs.io/)

### Installation
Run the Rally installation script (in home directory):
```shell
curl https://raw.githubusercontent.com/openstack/rally/master/install_rally.sh | bash
```
Rally will be installed in a virtualenv inside `~/rally`

## Using Rally
I'd recommend running Rally inside a Screen
```shell
screen
```
Activate Rally 
```shell
. ~/admin
. ~/rally/bin/activate
. ~/devstack/openrc admin admin
```

Create/use Rally deployment. If you haven't created a deployment yet or want to create a new one:
```shell
rally deployment create --fromenv --name=<name-you-choose>
```
If you want to use an old deployment:
```shell
rally deployment list
rally deployment use <uuid-of-deployment-you-want-to-use-from-the-list>
```

Run benchmarks. Rally benchmarks are highly configurable, and are either in the json or yaml format.

You can take a look at the sample benchmarks in `~/rally/samples/tasks/scenarios/*` and in `~/rally/src/rally-jobs`. 
As an example we will run a benchmark that creates and deletes cinder volumes.
```shell
rally task start ~/rally/samples/tasks/scenarios/cinder/create-and-delete-volume-type.yaml
```
Generate results page. You can generate an HTML results page like so:
```shell
rally task report --out=<choose-report-name>.html --open
```
