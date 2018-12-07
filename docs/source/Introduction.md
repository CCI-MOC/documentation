# OpenShift Introduction

[UP](MOC-OpenShift-Text-&amp;-Screenshot-Tutorial.html)

**OpenShift** is the container service the Mass Open Cloud has deployed.  If you haven't thought about containers it is useful to read a [little bit about them](https://en.wikipedia.org/wiki/Operating-system-level_virtualization)

Specifically, OpenShift uses [docker containers](https://en.wikipedia.org/wiki/Docker_(software)).

Docker containers have minimal storage requirements and are **immutable**.

Being immutable implies that any information which contains state needs to be in persistent storage.

This is sometime described with the terms **ephemeral** (no attached storage) and **persistent** (attached storage).

OpenShift uses [kubernetes](https://en.wikipedia.org/wiki/Kubernetes) to orchestrate the deployment and management of the docker containers.

Additionally, OpenShift augments kubernetes with many features useful for enterprise, however this tutorial will concentrate on OpenShift's development/deployment process.  

