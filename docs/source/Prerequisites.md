### 1. Install` oc commands` by downloading the executables from [here](https://github.com/openshift/origin/releases)
The OpenShift Enterprise CLI exposes commands for managing your applications, as well as lower level tools to interact with each component of your system

### 2. Must have credentials for OpenShift. (Kaizen credentials also work for [Mass OpenShift](https://openshift.massopen.cloud/))

### 3. What is Oshinko?
The Oshinko project covers several individual applications which all focus on the goal of deploying and managing Apache Spark clusters on Red Hat OpenShift and OpenShift Origin.

With the Oshinko family of applications you can create, scale, and destroy Apache Spark clusters. These clusters can then be used by your applications within an OpenShift project by providing a simple connection URL to the cluster. There are multiple paths to achieving this: browser based graphical interface, command line tool, and a RESTful server.

To begin your exploration, it is recommend starting with the oshinko-webui application. However this is completely optional. You can manage Spark instances directly from OpenShift Console. The oshinko-webui is a self-contained deployment of the Oshinko technologies. An OpenShift user can deploy the oshinko-webui container into their project and then access the server with a web browser. Through the browser interface you will be able to manage Apache Spark clusters within your project.

Another important part of Oshinko to highlight is the oshinko-s2i repository and associated images which implement the source-to-image workflow for Apache Spark based applications. These images enable you to create full applications that can be built, deployed and upgraded directly from a source repository.

# Steps to spin Spark clusters.
### 1. Open your terminal.

### 2. Login into [Mass OpenShift](https://openshift.massopen.cloud/) cluster through oc
`oc login https://openshift.massopen.cloud -u <your-username>`

`$ oc login https://openshift.massopen.cloud/ -u singh.p@husky.neu.edu`

`Authentication required for https://openshift.massopen.cloud:443 (openshift)`

`Username: <your-username>`

`Password: `

`Login successful.`


`You don't have any projects. You can try to create a new project, by running`

    `oc new-project <projectname>`

### 3. Create a new project
`oc new-project <project-name>`

### 4. install all the Oshinko resources into your project.
This creates the latest versions of the Oshinko S2I (source-to-image) templates and the Oshinko Web UI application, as well as a ServiceAccount and RoleBinding needed for creation and management of Apache Spark clusters.


`oc create -f https://radanalytics.io/resources.yaml`

`serviceaccount "oshinko" created`

`rolebinding "oshinko-edit" created`

`template "oshinko-pyspark-build-dc" created`

`template "oshinko-java-spark-build-dc" created`

`template "oshinko-scala-spark-build-dc" created`

`template "oshinko-webui-secure" created`

`template "oshinko-webui" created`


### 5. Start the Oshinko Web UI application by running `oc new-app --template=oshinko-webui` on the terminal.

This is optional. We can definitely do without oshinko webui and rest api as we can manage Spark instance directly from OpenShift console. (I recommend to skip this step)

`$ oc new-app --template=oshinko-webui`

`--> Deploying template "winemap-namespace/oshinko-webui" to project winemap-namespace`


     `* With parameters:`

        `* SPARK_DEFAULT=`

        `* OSHINKO_WEB_NAME=oshinko-web`

        `* OSHINKO_WEB_IMAGE=radanalyticsio/oshinko-webui:stable`

        `* OSHINKO_WEB_ROUTE_HOSTNAME=`

        `* OSHINKO_REFRESH_INTERVAL=5`

### 6. At this point you can go to your OpenShift Console , at https://openshift.massopen.cloud/console/ explore the Oshinko Web UI (again optional).


useful links:  
* https://radanalytics.io/get-started
* https://elmiko.github.io/2016/11/16/spark-on-openshift-with-oshinko.html
* https://docs.openshift.com/container-platform/3.3/admin_solutions/user_role_mgmt.html#view-roles-users-cluster


