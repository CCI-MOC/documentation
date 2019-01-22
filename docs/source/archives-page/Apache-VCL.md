# Overview
Apache VCL will allow users to request temporary VMs on demand to utilize an isolated computing environments to run specific applications. VCL is an Apache project that handles the requesting and management of the on-demand VMs. Here we will be looking at using OpenStack as the virtualization back end for VCL. 

# Lab Setup
For testing we have OpenStack Grizzly deployed using Mirantis Fuel 5.1 on 5 Dell desktop machines. There is a separate server running as a VM (but not in OpenStack) installed with CentOS 6 that will serve as the VCL Controller. It is connected to the network that OpenStack is using for Public IPs. 

- OpenStack Controller: 192.168.3.2
- VCL Controller: 192.168.3.251

# Install
## VCL Controller
To install the VCL controller,I followed the instructions located [here](http://vcl.apache.org/docs/VCL232InstallGuide.html).
The install is very straight forward and easy provided you are connected to the internet. I installed all roles (DB,Web,Mgmt) on the same machine.
## OpenStack Integration



