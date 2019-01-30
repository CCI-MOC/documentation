This page will describe how to do a contribution to one of the many OpenStack projects. Most of the information here can also be found in the [OpenStack Developer's Guide](http://docs.openstack.org/infra/manual/developers.html)

# Setup
## Account Setup
A step by step guide to creating the required accounts is available [here](http://docs.openstack.org/infra/manual/developers.html#account-setup).

* [Launchpad](https://launchpad.net/) - OpenStack uses Launchpad for bug/blueprint/spec management.
* [OpenStack Foundation](https://www.openstack.org/join/) - Join OpenStack as a foundation member.
* [Gerrit](https://review.openstack.org/#/q/status:open) - Gerrit is the system used for Reviewing changes. Use you Launchpad account to Login. Upon first login you will be asked to select a username (pick wisely, can't be changed later). Sign the [ICLA](https://review.openstack.org/#/settings/agreements).

## Technical Setup
Install git-review from your package management solution of choice (ex. Homebrew for OS X, apt/yum/dnf/zypper for Linux, or even pip). If your account username is different than your Gerrit username you will need to configure git-review to use it. 
```
$ git config --global gitreview.username yourgerritusername
```

Clone the repo you want to work on, either from GitHub or from OpenStack. As an example I will be using Keystone. Enter the directory and run git-review. 

```
$ git clone https://git.openstack.org/openstack/keystone.git
$ cd keystone
$ git review -s
```

# Contributing
## Finding what to do
As mentioned previously, bugs are found in Launchpad, ex. for [Nova](https://bugs.launchpad.net/nova). They are ordered by importance, but you should probably use Advanced Search to order them by time, and filter it to include only Confirmed/Triaged bugs, which as assigned to Nobody. When you find something to work on, if it's a bug, assign yourself to the bug in Launchpad. 

You can also lurk in the IRC channels and ask people for work to do. Usually the PTLs are very welcoming to such requests for work.

## Making the change
Create a new branch in the repo with the bug number.
```
git checkout -b bug/XXXXXX
```
Start hacking, and maybe create a unit test to prove yourself that you solved the bug and to prevent it from regressing. Run the unit tests.
```
# To run the unit tests
$ tox
```
If all the unit tests are passing and you've fixed the bug. Commit.
The first line of the commit message will be the title of the change, after that write one paragraph or two describing the change. The lines must be wrapped to 79 characters. 
If you fixed the bug, write `Closes-Bug: XXXXXX` at the end of your message on a separate line.

```
$ git commit
```

Finally propose the change. In the output of the command you will find the link to your change.
```
$ git review
```

## Pleasing the reviewers
For every change to be accepted, usually 2 core reviewers must +2, and one of them must +W (approving the change).
When you receive feedback and you need to make changes to your commit, make the required changes to the branch and amend the commit. Repropose again. The change will be submitted as a second patchset, but the link will be the same. On a new patchset, all the reviews are reset, but the reviewers will receive an email notifying them. 
```
git commit --amend
git review
```

Jenkins will run CI jobs on your change to make sure everything works. For a change to be accepted, all voting jobs must pass.