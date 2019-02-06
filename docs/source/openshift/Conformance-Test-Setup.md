## Conformance Test Setup
[UP](Testing-OpenShift.html)

[Reference](https://github.com/openshift/svt/tree/master/conformance)

To install the Conformance tests:
 1. Clone the repository
```shell
	git clone https://github.com/openshift/svt.git
```
 1. Install go
```shell
download latest https://golang.org/dl/
wget https://storage.googleapis.com/golang/go1.6.3.linux-amd64.tar.gz
tar xzvf go1.6.3.linux-amd64.tar.gz
```
     - system wide install
```shell
	sudo mv go /usr/local/
```
     -  add system wide path
```shell
        sudo vi /etc/profile
```
     -  add this to bottom
```shell
export PATH=$PATH:/usr/local/go/bin
export GOPATH=/usr/local/go/dev
export PATH=$PATH:$GOPATH/bin
```
     -  reload
```shell
        source /etc/profile
```
     -  test go
```shell
        go version
```
 1. Install ginkgo
```shell
        go get github.com/onsi/ginkgo/ginkgo
```
 1. Install atomic-openshift-tests
```shell
atomic-openshift-excluder unexclude
yum -y install atomic-openshift-tests
atomic-openshift-excluder exclude
```
 1. Run the tests
```shell
	cd ./svt/conformance
        ./svt_conformance
```
