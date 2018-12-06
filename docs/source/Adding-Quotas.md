UP <https://github.com/CCI-MOC/moc-public/wiki/OpenShift>

Quotas are set to ensure that the docker containers conform to both usage and size restrictions of the cluster.  They are set on both the container level, the project level and sets of projects.  To set up a default quota for all new projects, search for self provisioning projects.  

See: <https://github.com/openshift/origin/tree/master/examples/project-quota>
<https://docs.openshift.org/latest/admin_guide/managing_projects.html#template-for-new-projects>

1) Get the previous template:

        oadm create-bootstrap-project-template -o yaml > project_template_20170609.yaml

2) Just for completeness, this returned:

        apiVersion: v1
        kind: Template
        metadata:
          creationTimestamp: null
          name: project-request
        objects:
        - apiVersion: v1
          kind: Project
          metadata:
            annotations:
              openshift.io/description: ${PROJECT_DESCRIPTION}
              openshift.io/display-name: ${PROJECT_DISPLAYNAME}
              openshift.io/requester: ${PROJECT_REQUESTING_USER}
            creationTimestamp: null
            name: ${PROJECT_NAME}
          spec: {}
          status: {}
        - apiVersion: v1
          groupNames:
          - system:serviceaccounts:${PROJECT_NAME}
          kind: RoleBinding
          metadata:
            creationTimestamp: null
            name: system:image-pullers
            namespace: ${PROJECT_NAME}
          roleRef:
            name: system:image-puller
          subjects:
          - kind: SystemGroup
            name: system:serviceaccounts:${PROJECT_NAME}
          userNames: null
        - apiVersion: v1
          groupNames: null
          kind: RoleBinding
          metadata:
            creationTimestamp: null
            name: system:image-builders
            namespace: ${PROJECT_NAME}
          roleRef:
            name: system:image-builder
          subjects:
          - kind: ServiceAccount
            name: builder
          userNames:
          - system:serviceaccount:${PROJECT_NAME}:builder
        - apiVersion: v1
          groupNames: null
          kind: RoleBinding
          metadata:
            creationTimestamp: null
            name: system:deployers
            namespace: ${PROJECT_NAME}
          roleRef:
            name: system:deployer
          subjects:
          - kind: ServiceAccount
            name: deployer
          userNames:
          - system:serviceaccount:${PROJECT_NAME}:deployer
        - apiVersion: v1
          groupNames: null
          kind: RoleBinding
          metadata:
            creationTimestamp: null
            name: admin
            namespace: ${PROJECT_NAME}
          roleRef:
            name: admin
          subjects:
          - kind: User
            name: ${PROJECT_ADMIN_USER}
          userNames:
          - ${PROJECT_ADMIN_USER}
        parameters:
        - name: PROJECT_NAME
        - name: PROJECT_DISPLAYNAME
        - name: PROJECT_DESCRIPTION
        - name: PROJECT_ADMIN_USER
        - name: PROJECT_REQUESTING_USER

3) Here is a new project-template with limits:

        {
            "kind": "Template",
            "apiVersion": "v1",
            "metadata": {
                "name": "project-request",
                "creationTimestamp": null
            },
            "objects": [
                {
                    "kind": "Project",
                    "apiVersion": "v1",
                    "metadata": {
                        "name": "${PROJECT_NAME}",
                        "creationTimestamp": null,
                        "annotations": {
                            "openshift.io/description": "${PROJECT_DESCRIPTION}",
                            "openshift.io/display-name": "${PROJECT_DISPLAYNAME}",
                            "openshift.io/requester": "${PROJECT_REQUESTING_USER}"
                        }
                    },
                    "spec": {},
                    "status": {}
                },
                {
                    "apiVersion": "v1",
                    "kind": "ResourceQuota",
                    "metadata": {
                        "name": "object-counts"
                    },
                    "spec": {
                        "hard": {
                            "replicationcontrollers": "50",
                            "services": "10",
                            "secrets": "20",
                            "persistentvolumeclaims": "2"
                        }
                    }
                },
                {
            "apiVersion": "v1",
                    "kind": "ResourceQuota",
                    "metadata": {
                        "name": "compute-resources"
                    },
                    "spec": {
                        "hard": {
                            "limits.cpu": "4",
                            "limits.memory": "2Gi"
                        },
                        "scopes": [
                            "NotTerminating"
                        ]
                    }
                },
                {
                    "apiVersion": "v1",
                    "kind": "ResourceQuota",
                    "metadata": {
                        "name": "compute-resources-timebound"
                    },
                    "spec": {
                        "hard": {
                            "limits.cpu": "3",
                            "limits.memory": "1536Mi"
                        },
                        "scopes": [
                            "Terminating"
                        ]
                    }
                },
                {
                    "apiVersion": "v1",
                    "kind": "LimitRange",
                    "metadata": {
                        "creationTimestamp": null,
                        "name": "resource-limits"
                    },
                    "spec": {
                        "limits": [
                            {
                                "type": "Pod",
                                "max": {
                                    "cpu": "2",
                                    "memory": "1Gi"
                                },
                                "min": {
                                    "cpu": "29m",
                                    "memory": "150Mi"
                                }
                            },
                            {
                                "type": "Container",
                                "default": {
                                    "cpu": "1",
                                    "memory": "512Mi"
                                },
                                "defaultRequest": {
                                    "cpu": "60m",
                                    "memory": "307Mi"
                                },
                                "max": {
                                    "cpu": "2",
                                    "memory": "1Gi"
                                },
                                "min": {
                                    "cpu": "29m",
                                    "memory": "150Mi"
                                }
                            },
                            {
                                "type": "PersistentVolumeClaim",
                                "min": {
                                    "storage": "1Gi"
                                },
                                "max": {
                                    "storage": "1Gi"
                                }
                            }
                        ]
                    }
                },
                {
                    "kind": "RoleBinding",
                    "apiVersion": "v1",
                    "metadata": {
                        "name": "admin",
                        "namespace": "${PROJECT_NAME}",
                        "creationTimestamp": null
                    },
                    "userNames": [
                        "${PROJECT_ADMIN_USER}"
                    ],
                    "groupNames": null,
                    "subjects": [
                        {
                            "kind": "User",
                            "name": "${PROJECT_ADMIN_USER}"
                        }
                    ],
                    "roleRef": {
                        "name": "admin"
                    }
                },
                {
                    "kind": "RoleBinding",
                    "apiVersion": "v1",
                    "metadata": {
                        "name": "system:image-pullers",
                        "namespace": "${PROJECT_NAME}",
                        "creationTimestamp": null
                    },
                    "userNames": null,
                    "groupNames": [
                        "system:serviceaccounts:${PROJECT_NAME}"
                    ],
                    "subjects": [
                        {
                            "kind": "SystemGroup",
                            "name": "system:serviceaccounts:${PROJECT_NAME}"
                        }
                    ],
                    "roleRef": {
                        "name": "system:image-puller"
                    }
                },
                {
                    "kind": "RoleBinding",
                    "apiVersion": "v1",
                    "metadata": {
                        "name": "system:image-builders",
                        "namespace": "${PROJECT_NAME}",
                        "creationTimestamp": null
                    },
                    "userNames": [
                        "system:serviceaccount:${PROJECT_NAME}:builder"
                    ],
                    "groupNames": null,
                    "subjects": [
                        {
                            "kind": "ServiceAccount",
                            "name": "builder"
                        }
                    ],
                    "roleRef": {
                        "name": "system:image-builder"
                    }
                },
                {
                    "kind": "RoleBinding",
                    "apiVersion": "v1",
                    "metadata": {
                        "name": "system:deployers",
                        "namespace": "${PROJECT_NAME}",
                        "creationTimestamp": null
                    },
                    "userNames": [
                        "system:serviceaccount:${PROJECT_NAME}:deployer"
                    ],
                    "groupNames": null,
                    "subjects": [
                        {
                            "kind": "ServiceAccount",
                            "name": "deployer"
                        }
                    ],
                    "roleRef": {
                        "name": "system:deployer"
                    }
                }
            ],
            "parameters": [
                {
                    "name": "PROJECT_NAME"
                },
                {
                    "name": "PROJECT_DISPLAYNAME"
                },
                {
                    "name": "PROJECT_DESCRIPTION"
                },
                {
                    "name": "PROJECT_ADMIN_USER"
                },
                {
                    "name": "PROJECT_REQUESTING_USER"
                }
            ]
        }

4) to load the new project template:

        oc create -f project_template.yaml -n default

5) Change the master-config to refer to the new project-template:

        ...
        projectConfig:
          projectRequestTemplate: "default/project-request"
          ...

6) Restart the masters (in this case both masters)

        systemctl restart atomic-openshift-master-api
        systemctl restart atomic-openshift-master-controllers

