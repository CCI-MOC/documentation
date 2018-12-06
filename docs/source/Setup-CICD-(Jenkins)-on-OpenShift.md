# Setup CICD (Jenkins/GoCD) on OpenShift

**OpenShift Master**: v3.9.0

## Job of deployment pipeline:
Pipelines allow teams to automate and organize all of the activities required to deliver software changes. By rapidly providing visible feedback, teams can respond and react to failures quickly. 

## What problem does Jenkins solve exactly?
At a very high level, Jenkins CI/CD, ensure the quality and functionality of the codebase as developers constantly add to it.

* listens for new Pull Requests (finished features that are awaiting testing)
* runs a couple of automated test suites on the code which now includes the work branch
* deploys the latest code changes to a QA environment for manual testing

**CICD** - Containing our Jenkins instance

**Development** - Application Development

**Testing** - Code testing

**Production** - Deployment
 
## 1. Login with your OpenShift user credentials

Make sure you have OpenShift Command Line Tools and login
Get Started with the CLI

## 2. Run pipeline automation command (Python)
[openshift-pipeline.py](https://github.com/jadedh/openshift-cicd-pipeline/tree/master/openshift-pipeline/openshift-pipeline)

``` python openshift-pipeline.py --cicd [jenkins/gocd] [cicdname] --appname [appname] --stages [stages ...] [--command (optional)]```


Note: **app-name** must be consistent with you github repo name. 

What the setup script does...

1. Create projects for your OpenShift account
2. Add Role-Based Access Control. Allow individual projects to interact with each other
3. Add service accounts. Adds ability to pull images from the development project
4. Deploy Jenkins
5. **Generate pipeline template**. Stages can be customized to fits with the projects needs

## 3. Add your applications to the stages
(on the following projects: development, testing, production)

``` sh
oc new-app [your github repository]
```
sample repo: [myphp](https://github.com/jadedh/bgdemo)

## 4. Pipeline
Make sure you are in **cicd** project & the same local directory as **openshift-template.yaml**
``` sh
oc new-app openshift-template.yaml
```

## 5. Configure Jenkins

[Autheticate source code management on jenkins](https://wiki.jenkins.io/display/JENKINS/Github+Plugin)

[Configure jenkins on your github repo](https://blog.tentamen.eu/jenkins-and-github-integration-using-webhooks/)


Helpful Links:
[Create Your Own Build Pipelines with OpenShift 3.3](https://www.youtube.com/watch?v=07-Xx73y3zA)