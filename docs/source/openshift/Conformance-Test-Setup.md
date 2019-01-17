# Conformance Test Setup

[UP](Testing-OpenShift.html)

[Reference](https://github.com/openshift/svt/tree/master/conformance)

To install the Conformance tests:
1. Clone the repository

	git clone https://github.com/openshift/svt.git

2. Install go
```
download latest https://golang.org/dl/
wget https://storage.googleapis.com/golang/go1.6.3.linux-amd64.tar.gz
tar xzvf go1.6.3.linux-amd64.tar.gz
```
  * system wide install

	sudo mv go /usr/local/

  * add system wide path

        sudo vi /etc/profile

  * add this to bottom
```
export PATH=$PATH:/usr/local/go/bin
export GOPATH=/usr/local/go/dev
export PATH=$PATH:$GOPATH/bin
```
  * reload

        source /etc/profile

  * test go

        go version

3. Install ginkgo

        go get github.com/onsi/ginkgo/ginkgo

4. Install atomic-openshift-tests
```
atomic-openshift-excluder unexclude
yum -y install atomic-openshift-tests
atomic-openshift-excluder exclude
```
5. Run the tests

	cd ./svt/conformance
        ./svt_conformance

