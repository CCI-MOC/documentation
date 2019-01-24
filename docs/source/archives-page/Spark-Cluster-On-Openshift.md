Before starting this step, there are a few prerequisites which are necessary to ensure you have the proper tools and resources to complete the work.

Access to an OpenShift cluster (which is https://openshift.massopen.cloud)

A terminal with the oc command available

A new project in OpenShift to store your work. (You created this in step 3 above).  

The Oshinko templates available in your project. (Step 4)

An editor to work on your source files

An online space for a Git repository to store your code, such as GitHub or BitBucket

With these in place you are ready to begin creating your microservice.

# Building SparkPi in Python with Flask

These instructions will help you to create a SparkPi microservice using the Python language and the Flask framework.

### 1. Create the application source file
This application is relatively compact and will only need a single source file for the implementation we have chosen. By default, the Python language source-to-image builder will look for a file named app.py in the root of your repository as the entry point for the application. This can be modified by passing a parameter to the build command, but for now let’s keep things simple and create this file.

Fork this project https://github.com/husky-parul/sparkpi.git

### 2. Add dependencies
There is one additional file that we will need to make our application work. If you are familiar with Python dependency management then you may have seen requirements.txt files before. This file is used by the source-to-image builder to install any extra dependencies we may need. Since we are using Flask for our HTTP framework, and it is not part of Python’s default packages, we will need to install it through this file.

Create a file named requirements.txt in the root of your project and add the following contents. This line will ensure that the proper version of Flask is installed into our application image. (It's already present in the git repo you forked.)

### 3. Commit your code
The last step before we can build and run our application is to check in the files and push them to your repository. If you have followed the setup instructions and cloned your repository from an upstream of your creation, this should be as simple as running the following commands:

    git add .
    git commit -m "add initial files"
    git push

Make sure to note the location of your remote repository as you will need it in the next step.

### 4. Build and run the application

Now that all your files have been created, checked in and pushed to your online repository you are ready to command OpenShift to build and run your application. The following command will start the process, you can see that we are telling OpenShift to use the oshinko-pyspark-build-dc template for our application. This template contains the necessary components to invoke the Oshinko source-to-image builder. We also give our application a name and tell the builder where to find our source code. Issue the following command, making sure to enter your repository location for the **GIT_URI parameter**:


`$ oc new-app --template oshinko-pyspark-build-dc -p APPLICATION_NAME=sparkpi -p GIT_URI=YOUR_REPOSITORY_URI_HERE `


     --> Deploying template "pi/oshinko-pyspark-build-dc" to project pi

     PySpark
     ---------
     Create a buildconfig, imagestream and deploymentconfig using source-to-image and pyspark source hosted in git


     * With parameters:
        * Application Name=sparkpi
        * Git Repository URL=https://github.com/radanalyticsio/tutorial-sparkpi-python-flask.git
        * Application Arguments=
        * spark-submit Options=
        * Git Reference=
        * OSHINKO_CLUSTER_NAME=
        * OSHINKO_NAMED_CONFIG=
        * OSHINKO_SPARK_DRIVER_CONFIG=
        * OSHINKO_DEL_CLUSTER=true
        * APP_FILE=

       --> Creating resources ...

        imagestream "sparkpi" created

        buildconfig "sparkpi" created

        deploymentconfig "sparkpi" created

        service "sparkpi" created
        
        --> Success

        Build scheduled, use 'oc logs -f bc/sparkpi' to track its progress.
        Run 'oc status' to view your app.

### 5. Your application is now being built on OpenShift!

A common task when building and running applications on OpenShift is to monitor the logs. You can even see a suggestion at the bottom of the oc new-app command output that suggests we run oc logs -f bc/sparkpi. Running this command will follow(-f) the BuildConfig(bc) for your application sparkpi. When you run that command you should see something that begins like this:

` $ oc logs -f bc/sparkpi`


    Cloning "https://github.com/radanalyticsio/tutorial-sparkpi-python-flask.git" ...
        Commit: c8dbb96247c51ea8f13a7dfcf38fc37221378bbe (convert range to xrange)
        Author: Michael McCune <msm@redhat.com>
        Date:   Thu Aug 24 10:48:19 2017 -0400
    Pulling image "radanalyticsio/radanalytics-pyspark" ...
       ---> Installing application source ...
        ---> Installing dependencies ...
    You are using pip version 7.1.0, however version 9.0.1 is available.
    You should consider upgrading via the 'pip install --upgrade pip' command.
    Collecting Flask==0.12.1 (from -r requirements.txt (line 1))
    ...

The output from this call may be quite long depending on the steps required to build the application, but at the end you should see the source-to-image builder pushing the newly created image into OpenShift. You may or may not see all the "Pushed" status lines due to output buffer logging, but at the end you should see "Push successful", like this:

`$ oc logs -f dc/sparkpi`


    --> Scaling sparkpi-1 to 1
    --> Waiting up to 10m0s for pods in rc sparkpi-1 to become ready
    --> Success

If you see this output, it just means that you have caught the logs before the DeploymentConfig has generated anything from your application. Run the command again and you should start to see the output from the application, which should be similar to this:

`$ oc logs -f dc/sparkpi`



    version 1
        Didn't find cluster cluster-qlcvtk, creating ephemeral cluster
        Using ephemeral cluster cluster-qlcvtk
        Waiting for spark master http://cluster-qlcvtk-ui:8080 to be available ...
        Waiting for spark master http://cluster-qlcvtk-ui:8080 to be available ...
        Waiting for spark master http://cluster-qlcvtk-ui:8080 to be available ...
        Waiting for spark master http://cluster-qlcvtk-ui:8080 to be available ...
        Waiting for spark master http://cluster-qlcvtk-ui:8080 to be available ...
        Waiting for spark workers (1/0 alive) ...
        Waiting for spark workers (1/1 alive) ...
        All spark workers alive
        spark-submit --master spark://cluster-qlcvtk:7077 /opt/app-root/src/app.py
        * Running on http://0.0.0.0:8080/ (Press CTRL+C to quit)

   



Let’s break this down a little. These first few lines are actually being generated by the Oshinko source-to-image tooling. They show that no Apache Spark cluster has been specified for the application, and as such it must create an ephemeral cluster. It then waits for the cluster to become fully active before launching the application.

On the last two lines you see the spark-submit command which will run the application and the output from Flask informing us that it is listening on the host and port we specified.

### 6. Become a user
At this point you should have created a code repository for your microservice, populated the repository with source files, built an application image and launched that image on OpenShift. The final stage in this tutorial is to expose your microservice outside of OpenShift and begin interacting with it as a user would.

The first step in this process is to expose a route to your microservice. OpenShift contains an edge router which will associate domain name URIs with services. By default, applications you create through source-to-image have services that can expose routes which will contain their name, the project name and a hostname for the OpenShift server. You can create these routes by using the following command:

    oc expose svc/sparkpi

To see the routes available in your project, run oc get routes, you should see something like this:

     $ oc get route
     NAME                      HOST/PORT                                      PATH      SERVICES            PORT       
     TERMINATION   WILDCARD
     cluster-uo7wa9-ui-route   cluster-uo7wa9-ui-route-pi.10.0.1.109.xip.io             cluster-uo7wa9-ui   <all>                    
       None
       sparkpi                 sparkpi-pi.10.0.1.109.xip.io                              sparkpi            8080-tcp                 
       None
