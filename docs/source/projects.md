UP: <https://github.com/CCI-MOC/moc-public/wiki/Useful-OpenShift-Commands>

Joining Project Networks allows project1 and project2 to access any pods and services in the other:

        oadm pod-network join-projects --to=<project1> <project2>

Isolate project networks:

        oadm pod-network isolate-projects <project1> <project2>

make a project global, that is project1 can access all other projects and all other projects can access project1:

        oadm pod-network make-projects-global <project1>

