## OpenShift Introduction

**OpenShift** is the container service the Mass Open Cloud has deployed.  

If you haven't heard of containers it is useful to read about them: (https://en.wikipedia.org/wiki/Operating-system-level_virtualization)

Specifically, OpenShift uses docker containers(https://en.wikipedia.org/wiki/Docker_(software)).

Docker containers have minimal storage requirements and are **immutable**.  Basically, making a change to one container will
not change all of the containers of that type.  If you should change a container and redeploy the same container, your 
changes would not be kept.  Thus being immutable implies that any information which contains state needs to be in persistent storage.

Storage for a container can either be describe as **ephemeral** or **persistent**.  Although they are both attached to the container, ephemeral storage will be released when the container is stopped.  However, persistent storage will remain
after the container is release.

OpenShift uses [kubernetes](https://en.wikipedia.org/wiki/Kubernetes) to orchestrate the deployment 
and management of the docker containers.  

Additionally, OpenShift augments Kubernetes with many features useful for enterprise.  From a developer's perspective, the source to image (s2i) build process is very convenient to start developing applications quickly. 
