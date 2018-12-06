UP: <https://github.com/CCI-MOC/moc-public/wiki/OpenShift>

Limiting the number of projects a user can have.

On each master do the following:

1) Ensure the new-project-template has the following line:
    objects: kind: Project, metadata: annotations: {..."openshift.io/requester": "${PROJECT_REQUESTING_USER}"...}

2) Add the following to the /etc/origin/master/master-config.yaml file:

        admissionConfig:
          pluginConfig:
            ProjectRequestLimit:
              configuration:
                apiVersion: v1
                kind: ProjectRequestLimitConfig
                limits:
                - selector:
                    level: admin
                - selector:
                    level: advanced
                  maxProjects: 10
                - maxProjects: 2

    This means that user accounts labeled with admin can create an unlimited number of projects, user accounts labeled with "advance" can create 10 projects, and all other user accounts can create only 2 projects.

3) restart the master:

        systemctl restart atomic-openshift-master-api
        systemctl restart atomic-openshift-master-controllers 