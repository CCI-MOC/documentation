[UP]https://github.com/CCI-MOC/moc-public/wiki/MOC-OpenShift-Text-%26amp%3B-Screenshot-Tutorial

OpenShift is the container service the Mass Open Cloud has deployed.  If you haven't thought about containers it is useful to read a little bit about them in:

  https://en.wikipedia.org/wiki/Operating-system-level_virtualization

Specifically, OpenShift uses docker containers (https://en.wikipedia.org/wiki/Docker_(software)).  Docker containers have minimal storage requirements and are immutable.  Being immutable implies that any information which contains state needs to be in persistent storage - this is sometime described with the terms ephemeral (no attached storage) and persistent (attached storage).

OpenShift uses kubernetes to orchestrate the deployment and management of the docker containers (https://en.wikipedia.org/wiki/Kubernetes).  Additionally, OpenShift augments kubernetes with many features useful for enterprise, however this tutorial will concentrate on OpenShift's development/deployment process.  

 