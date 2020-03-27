## How to Deploy a project Using OpenShift CLI

#### Step 1

           oc new-app /path/to/source/code
           oc new-app https://github.com/sclorg/s2i-ruby-container.git \
          --context-dir=2.0/test/puma-test-app

#### Step 2

Expose the application created so it’s available outside of the OpenShift cluster.

           oc expose service/<nameoftheapplication>

#### Step 3

Access the route

            oc get route/<name of the application>

#### Step 4

Build configuration of the application

            oc edit bc <name of application>

