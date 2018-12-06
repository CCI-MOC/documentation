<!-- linky links -->
[CLI Cheat Sheet]: http://docs.openstack.org/user-guide/cli-cheat-sheet.html

## Introduction

The OpenStack CLI is designed for interactive use.  It's also possible to call it from a bash script or similar, but typically it is too slow for heavy scripting use.
  

## Command Line setup

To use CLI, you must set some environmental variables.  The easiest way to do this is to run the OpenStack RC script you downloaded earlier.  Find the file (by default it will be named `project-openrc.sh` where project is the name of your OpenStack project).  Source the file:

    [kamfonik@ezio ~]$ source tutorial_project-openrc.sh
    Please enter your OpenStack Password:

You will be prompted for the password to your OpenStack account.  Note that this just stores your entry into the environment variable - there's no validation at this stage.  If you have trouble authenticating later, try running the script again and re-enter your password, in case you made a typo.

What this script does is set the following environment variables:

    OS_AUTH_URL       # the URL endpoint to interact with Keystone
    OS_TENANT_ID      # the ID of your OpenStack project
    OS_TENANT_NAME    # the name of your OpenStack project
    OS_PROJECT_NAME   # the name of your OpenStack project
    OS_USERNAME       # your username
    OS_PASSWORD       # your password
    OS_REGION_NAME    # OpenStack region name, which in our case is 'MOC_Kaizen'

Note that "project" and "tenant" both refer to the same thing, your OpenStack project.

## OpenStack Hello World

To test that you have everything configured, try out some commands.  The following command lists all the images available to your project:

    [kamfonik@ezio ~]$ openstack image list
    +--------------------------------------+------------------------------------------+--------+
    | ID                                   | Name                                     | Status |
    +--------------------------------------+------------------------------------------+--------+
    | 64599610-2952-4a1f-9291-2711c966905c | Sahara: Vanilla-MOCRemix on Ubuntu 14.04 | active |
    | df9982fb-aac7-48d4-ad78-93c3105a5d68 | Sahara: Storm 0.9.2 on Ubuntu 14.04      | active |
    | 0fd4d588-77de-4567-afc4-7433ad94fb98 | Sahara: Vanilla 2.7.1 on Ubuntu 14.04    | active |
    | d47e31d3-50e4-4c73-a65c-db17c423e044 | Sahara: Vanilla 2.7.1 on CentOS 6.6      | active |
    | 599fdf40-1fd9-4cda-9d9b-8d91e76d6195 | Sahara: Spark 1.3.1 on Ubuntu 14.04      | active |
    | 0f613a03-a85b-4ce5-be65-2431d9715040 | centos_bmi                               | active |
    | 454d6106-bb8d-4cc4-a8e7-1c0c6ed126ef | centos-6.7                               | active |
    | 3dae6817-36bb-4476-b68f-e743ae5490f3 | ubuntu16.04                              | active |
    | bd62fbfb-937b-4e2c-b4c1-99c7a80841f4 | hortonworks-sandbox                      | active |
    | 07944e19-2474-47e6-8412-57f2c1826570 | cirros-0.3.4-x86_64-disk.img_alt         | active |
    | 30bbd7e6-3a33-4cc3-8cb7-35b797617ac9 | cirros-0.3.4-x86_64-disk.img             | active |
    | 3dfb6cd0-9bf8-4106-b6ef-e735542fb669 | ubuntu-14.04                             | active |
    | 895a5bd0-9508-4c39-ab1c-b19337e61068 | ubuntu-15.10                             | active |
    | 79e50158-1526-4602-b859-6465986e942c | RHEL7.1                                  | active |
    | afba46bb-8be3-4d0f-a845-a3f8ba1cd596 | Centos 7 -RAW                            | active |
    | ef8d3b1d-8639-4e3b-9129-a9aad2c717a1 | Centos 7                                 | active |
    +--------------------------------------+------------------------------------------+--------+


If you have launched some instances already, the following command shows a list of your project's instances:

    [kamfonik@ezio Downloads]$ openstack server list
    +--------------------------------------+------------------+---------+---------------------------------------+
    | ID                                   | Name             | Status  | Networks                              |
    +--------------------------------------+------------------+---------+---------------------------------------+
    | 0ec026ff-1014-4953-9660-0b6386915562 | test             | ACTIVE  | kamfonik-net=10.20.8.15               |
    | 5e1a685b-db75-4af1-8b1a-c077c1e8b9e4 | kamfonik-gw      | ACTIVE  | kamfonik-net=10.20.8.9,               |
    | 34898710-8cf9-44db-9212-32539876e17c | kamfonik-gateway | SHUTOFF | kamfonik-net=10.20.8.8                |
    | c04b34d9-b684-40a3-9873-b1bd7daf08bf | inventory-dev    | ACTIVE  | kamfonik-net=10.20.8.5                |
    +--------------------------------------+------------------+---------+---------------------------------------+
  
If you don't have any instances, you will get the error `list index out of range`, which is why we didn't suggest this command for your first test:

    [kamfonik@ezio ~]$ openstack server list
    list index out of range

If you see this error:
   
    [kamfonik@ezio moc-public.wiki]$ openstack server list
    The request you have made requires authentication. (HTTP 401) (Request-ID: req-6a827bf3-d5e8-47f2-984c-b6edeeb2f7fb)

Then your environment variables are likely not configured correctly.  The most common reason is that you made a typo when entering your password.  Try sourcing the rc script again and retyping it.

You can type `openstack -h` to see a list of available commands.  There is also a useful [CLI cheat sheet] maintained in the End User guide. 

Note that this includes some admin-only commands. If you try one of these by mistake, you might see this output:

    [kamfonik@ezio ~]$ openstack user list
    You are not authorized to perform the requested action: admin_required (HTTP 403) (Request-ID: req-60ceb1b8-a90f-407a-84dd-66e0d4b40869)

Depending on your needs for API interaction, this  might be sufficient.  If you just occasionally want to run 1 or 2 of these commands from your terminal, you can do it manually or write a quick bash script that makes use of this CLI.  However, this isn't a very optimized  way to do complex interactions with OpenStack.  For that, you want to write scripts that interact with the python bindings directly.

Pro Tip: If you find yourself fiddling extensively with awk and grep to extract things like project IDs from the CLI output, it's time to move on to using the client libraries or the RESTful API directly in your scripts.

***

#### Next: [[Python SDK]]
##### Previous: [[API Access]]
[[Openstack Tutorial Index]]
