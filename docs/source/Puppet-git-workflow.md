This document outlines the workflow for adding new changes to our production puppet modules.

##0. Create your own Puppet environment on the staging-area Foreman to use for development and testing.
* `$ cd /etc/puppet/environments/[your_environment]`
* `$ git clone https://github.com/CCI-MOC/kilo-puppet`
* *(add steps here for associating the environment to some test VMs)*
* You should only have to do this step once.  You can reuse this development environment and associated VMs, and just use git branches to keep track of your work.

##1. Create your own local branch from the current master
* `$ git checkout master`
* `$ git pull origin master`
* `$ git checkout -b [new_branch_name]`
* Write your code...

##2. Perform local testing in the virtual staging area
* `$ puppet parser validate [new_file_name]`
* `$ puppet apply [root_manifest_name] --noop
* ssh to your testing VMs
     * `puppet agent -t` on each VM
* Is everything working?
     * **NO**: Go back to step 1.
     * **YES**: Yay! continue with the rest of Step 2.
* If your testing/development spans enough time, make sure you keep merging in up-to-date copies of master.  
     * `$ git fetch --all`
     * `$ git merge master`
* Once everything works, make sure you have committed all your changes to the local branch, and push the branch to the remote repo:
     * `$ git push origin [new_branch_name ***NOT MASTER***]`

##3. Sign out the staging area
* Current staging area nodes are compute-25 (controller) and compute-46 (compute).
* Check the [sign out sheet](https://docs.google.com/spreadsheets/d/1-L8TqGJqpRtmy418js0fOsbFLBP-l2xdES1XzsceRik/edit?usp=sharing) to see if staging area is in use.
* If it is free, sign it out by adding your name, a brief description of what you are testing, and the date.    
* If it is signed out, check with the person about whether they are still using it, and when they will be done. 
     * Do not continue until you have confirmed the staging area is free, then signed it out yourself.

##4. Test your code in the physical staging area
##### NOTE: ONLY ONE CHANGE CAN BE TESTED IN THE STAGING AREA AT A TIME - SEE STEP 3
* cd to the staging-area environment folder
* `$ git fetch --all`
* `$ git checkout -b origin [new_branch_name]`
* If you are unable to check out your branch because someone has left uncommitted changes in theirs, commit the changes to their local branch.
* Deploy the code on the staging area nodes:
     * ssh to compute 25 
     * `$ puppet agent -t`
     * ssh to compute 46
     * `$ puppet agent -t`
* Make debugging edits and deploy again until the code works. 
     * Check to make sure you have merged all recent changes to master
     * Commit all changes to [new_branch_name]
* `$ git push origin [new_branch_name ***NOT MASTER***]`

##6. Run Tempest
* If your code might affect OpenStack in any way, you must run Tempest to make sure it works.

##7. Once your code passes Tempest, submit a pull request to our master branch
* Include a brief description of:
    * what the change does
    * what testing you did
    * results of the tests

* Two engineers need to +1 the changes
* The second engineer to +1 will merge the changes and run tempest again in the staging area, using the new master branch, to make sure no problems were introduced by updates to master that happened after you last ran tempest.

##8. Once the changes are merged and have passed Tempest, pull down the new master in the production environment
* ssh to foreman vm
* `$ cd /etc/puppet/environments/production/modules`
* `$ git fetch --all`
* `$ git pull origin master`

