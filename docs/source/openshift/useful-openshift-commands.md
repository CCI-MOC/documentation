# Useful OpenShift Commands

1. Log in:

    - Visit the web console
    - Select "Copy login command" from the account menu in the upper right
    - Paste the generated `oc login` command in to your terminal

       ```shell
       oc login --token=... --server=https://...
       ```

1. List the projects:

    ```shell
    oc projects
    ```
1. Create a project:

    ```shell
    oc new-project <project name> --description="<project description>"
    ```
1. Get current project:

    ```shell
    oc project
    ```
1. Set current project:

    ```shell
    oc project <project name>
    ```
1. List pods in current project:

    ```shell
    oc get pods
    ```
1. describe a pod:

    ```shell
    oc describe pod <pod name>
    ```
1. Show log from a pod:

    ```shell
    oc logs <pod name>
    ```

1. Start a shell in a pod:

    ```
    oc exec -it <pod name> -- bash
    ```
