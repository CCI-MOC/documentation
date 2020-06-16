# To Do List for New MOC Intern and Member

## Things to do

### For everyone

1. Be able to run IRC and Slack
  * `#moc`, `#openinfralabs`, `#openinfralabs-monitoring` on freenode.

  (usually folks use ZNC bouncer, some of us use irccloud which as a built in bouncer - #openinfralabs is archived so a bouncer may not be needed.)


2. Checkout docs.massopen.cloud
  If you find things which may be improved you are encouraged to do a pull request and fix them.
  Understand how to use SSH and generate their public keys.


3. Share contact info and subscribe to the team mailing list.
    *  Add your name and contact information at [this URL](../contacts/People.html)
    *  Subscribe to the [team mailing lists](https://lists.massopen.cloud)

4. Trello: Send mail to msd@bu.edu with your Trello user name and you will be added to the appropriate organization.


### For Support Types

Some of the items below require levels of access that must be earned over time, based on proving knowledge and judgement.  Leveling Up is encouraged!

Please familiarize yourself with the following topics:

1. [OSTicket](https://osticket.massopen.cloud/)
    * Look at the [knowledgebase](https://osticket.massopen.cloud/kb/index.php) to know what's already covered.

2. Terminology
    * [Kaizen](https://kaizen.massopen.cloud)
        - The MOC team tutorial on how to use our production system can be found at (here)[https://example.com]
    * [Openshift](https://k-openshift.osh.massopen.cloud:8443)
    * Kubernetes
    * [Engage1](https://engage1.massopen.cloud)
    * [Openstack](https://www.openstack.org/)
        - [Getting started guide](http://docs.openstack.org/admin-guide-cloud/content/ch_getting-started-with-openstack.html) can be helpful.
    * Version of SW
        - How to tell which Openstack
        - How to tell which Openshift
    * [Ceph](https://ceph.io/)
    * [Object Store](https://en.wikipedia.org/wiki/Object_storage)
        - [S3](https://aws.amazon.com/s3/)
        - [Openstack Swift](https://wiki.openstack.org/wiki/Swift)
    * [Block Storage](https://en.wikipedia.org/wiki/Block_(data_storage)
        - [Ceph RBD](https://docs.ceph.com/docs/master/rbd/)
        - [Openstack Cinder](https://wiki.openstack.org/wiki/Cinder)
    * [Flavors](https://docs.openstack.org/nova/latest/user/flavors.html)
    * [RHEV](https://www.redhat.com/en/technologies/virtualization/enterprise-virtualization)
        - [MOC's deployment of RHEV](https://ovirt.massopen.cloud) is also called oVirt.
        - [oVirt](https://www.ovirt.org/) is the upstream project.
        - [PRB ovirt deployment](https://pov.massopen.cloud)

3. Openstack Tools
    * [Horizon](https://kaizen.massopen.cloud)
        - Watch online tutorials, read up documentation. Would recommend creating instances, routers, security groups etc at least once.
    * Command Line
        - How to configure on your computer

4. Monitoring System
    * [Zabbix Dashboard](https://zabbix.massopen.cloud/) navigation
    * Which zabbix URL for which information

5. [MAAS](https://maas.io/)
* Our deployment is at https://maas.massopen.cloud

6. HIL/BMI
    * HIL: https://github.com/cci-moc/hil
    * BMI/M2: https://github.com/cci-moc/m2
    * How to: https://docs.massopen.cloud/en/latest/clusters/kaizen/Kaizen-Bare-Metal.html

7. [FreeIPA](https://www.freeipa.org/)
    * Our deployment is at https://freeipa.infra.massopen.cloud/ipa/ui/

8. Access to RH MOC account
    * How to research for existing bugs
    * How to [open a ticket](https://access.redhat.com/)


### For Software Developers

1.  Python: You may want to consider using [iPython](http://ipython.org/) or other interactive Python tool to learn to code in Python.

2. Start by reviewing pull requests - if you click approve you are equally guilty as the person who wrote bad code that you approved

3. Suggestion: if writing python code, use some static analysis tools (e.g. pylint, black). Helpful in highlighting unused imports, variables, functions, bad return types etc before you submit your code for review.

4. Familiarize yourself with [GitHub](https://guides.github.com/) and git.

5. Automated testing (unit/integration tests) to confirm that your project works:
    * Python: pytest
    * TravisCI, Jenkins, CircleCI

## MOC Individual Contributor License Agreement (ICLA)
Contributors to the MOC are required to sign the 
[Individual Contributor Agreement](https://massopen.cloud/blog/individual-contributor-license-agreement/).
Send Jennifer Stacy (jstacy@bu.edu) a quick note when you are done with this step.
Note that you will not be allowed to contribute to any code base unless you sign the agreement.

## Team Communication
 -  **Standup** -  : the MOC team is an Agile team. Twice a week, Tues. and Thur., we have standup in the MOC team room. Face-to-face communication is best. However, if you cannot attend in person, you can dial in
 -  **Team Slack room** -  : For private team conversation, a good alternative channel of communication is the [massopencloud.slack.com](https://massopencloud.slack.com/). Please
 If you don't have a slack account, go to [slack.com](https://slack.com) and create one.
 If you do have a slack account already, you can access the **massopencloud** -  room as you wish. Contact msd@bu.edu if you have any problem.
 -  **MOC team email mailing list** -  : Go to the following the following URL to subscribe yourself to the 
 MOC [team mailing list](https://mail.massopen.cloud/mailman/listinfo/team)

Contact msd@bu.edu if you have any question/problem.

## MOC Community

We want everyone that has a strong engagement with the MOC to be visible to and engaged with our users.
 The goal is to keep the time commitment modest and spread across the entire team of developers, postdocs, visiting scientists, faculty and students,
 while maintaining a high level of service to Kaizen users.   

What we are asking each MOCer is to take on one day of the help desk per month.
 That is, you will be responsible for answering questions that come to the ticketing system for one day (as well as any follow up). 

The expectation isn’t that you a-priori know an answer, but that a smart person with insider access to the project team can quickly figure out an answer,
 put together a response and where it makes sense expand on our FAQ list.

To be part of the MOC community and participate in the help desk/ticketing system:
 -  send an email to admin@lists.massopen.cloud and ask to be added to both the signup sheet for the helpdesk and the MOC ticketing system.
 -  signup for your help desk coverage [here](http://www.signupgenius.com/go/5080444aca622abff2-help)
 -  subscribe to the [kaizen mailing list](https://mail.massopen.cloud/mailman/listinfo/kaizen)

This mailing list is where you can ask the experts or share your knowledge in anything related to the MOC infrastructure
including but not limited to OpenStack, Ceph and general networking topology in our production Kaizen.

For part-time interns, i.e., those that work 10-15 hours a week, this task is optional.
 You are encouraged to volunteer if this activity will not interfere with your class schedule.

You can expect at most a couple of questions in a day, and likely less. 
 If you get more than that, then contact admin@lists.massopen.cloud and we will get you more help.
 We think this will be a great learning experience for everyone, and help increase the engagements across the team.

## Special Interest Mailing Lists


`moc-research-list@bu.edu` : for those who are interested in research related activities in the MOC. 
The mailing list is currently not very active.

If you need help subscribing to this mailing list, instruction can be found under the first part of 
[this wiki](../archives-page/Outdated-To-do-list-for-New-MOC-Intern-Member.html)


## Kaizen User Account

**A user account for accessing the production Openstack.**
If you asked to register as a Kaizen user, go to this [URL](https://onboarding.massopen.cloud/signup/)
and follow instruction at the page to register for a new account in Kaizen - MOC OpenStack cloud.

*NOTE : Previous version of this wiki can be found [here](../archives-page/Outdated-To-do-list-for-New-MOC-Intern-Member.html)*
