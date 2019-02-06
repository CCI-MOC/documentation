## OpenShift Projects
[UP](Useful-OpenShift-Commands.html)

Joining Project Networks allows project1 and project2 to access any pods and services in the other:
```shell
        oadm pod-network join-projects --to=<project1> <project2>
```
Isolate project networks:
```shell
        oadm pod-network isolate-projects <project1> <project2>
```
make a project global, that is project1 can access all other projects and all other projects can access project1:
```shell
        oadm pod-network make-projects-global <project1>
```
