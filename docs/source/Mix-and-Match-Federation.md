# Overview
We are proposing a new set of use cases for OpenStack and a set of 
changes to enable a multi-landlord cloud model, where multiple service
providers can cooperate (and compete) to stand up services in a single
cloud. This wiki page first describes the model and then our plan for a 
end-to-end demo by the Tokyo summit. 

#Motivation and High-level use cases

All current clouds are stood up by a single company or organization,
creating a vertically integrated monopoly.  Any competition is between
entire clouds and is limited by the customer's ability to move their
data over the connectivity between clouds.  We think an alternative
model is possible, where different organizations can stand up
individual services in the same data centers, and the customer (or
intermediaries acting on their behalf) can pick and choose between
them.  We call this model of having multiple landlords in a cloud an
Open Cloud eXchange ([OCX](http://www.cs.bu.edu/fac/best/res/papers/ic14.pdf)).

The OCX model would enable more innovation by technology companies,
enable cloud research and provide more choice to cloud consumers. We
are developing this model in a new public cloud, the Massachusetts
Open Cloud (MOC), that is just being started in the
[MGHPCC](http://www.mghpcc.org) data center shared between Boston
University, Harvard University, the Massachusetts Institute of
Technology, Northeastern University, and the University of
Massachusetts.  Some use cases being explored in the context of the
MOC illustrate the potential of this model:

- Harvard and MIT both stand up their own OpenStack cloud for
  students, but provide resources on-demand to the MOC that can be used
  by researchers that collaborate between the universities and by
  external users.  
- A storage company stands up a new innovative block storage service
  on a few racks in the MGHPCC, operates it themselves, and makes it
  available to users of the MOC and/or the individual university
  clouds.  The storage company is in total control of price,
  automation, and SLA for the service, and users can choose if they
  want to use the service.
- A company stands up a new compute service with innovative hardware
  (e.g., FPGAs, crypto accellerator) or pricing model.  A customer
  with a two Petabyte disk volume can switch to using that compute
  without having to move the data.
- A research group at Boston University and Northeastern develops a
  highly elastic compute service that can checkpoint 1000s of servers
  in seconds into SSD, and broadcast provision a distributed
  application to allow an interactive medical imaging application that
  wants 1000s of servers for a few seconds. 
- A security sensitive life sciences company exploits software from
  the [MACS](http://www.bu.edu/hic/research/macs/) project to
  distribute their data across two storage services from non-colluding
  providers.  The data is accessed from a Nova compute service running
  on a trusted compute platform developed collaboratively with
  Intel. Since all services are deployed in the same datacenter, the
  computation is efficient.
- Students in a [cloud computing course](https://okrieg.github.io/EC500/index.html)  offered by Northeastern and 
  Boston University faculty
  develop and stand up an
  experimental proxy service for open stack services that provides
  users of the MOC a Swift service that combines the inventory of
  multiple underlying Swift services.

We believe solutions to the problems of the OCX model will improve
OpenStack generally from a security and reliability
perspective. Solving the problems of multiple providers/landlords that
don't trust each other also brings defense in depth for a single
provider cloud; potentially preventing an exploit of one service from
compromising an entire cloud.

We will be doing this work in the context of the newly annouced
[Mercador](https://wiki.openstack.org/wiki/Mercador) project.  The
Mercador focus was originally on federation of resourcs between
untrusting single provider clouds.  When the two teams met, we
realized that the two projects solve orthogonal but complementary
problems, and we decided by joining forces we can help ensure that the
(still orthogonal) development efforts don't come up with solutions
that are incompatible.

# Demo Plan
We plan to have an end-to-end demo by the Tokyo summit. We first outline use cases, 
then design assumptions, then details on each use case

## Low-level use cases
- boot image from Openstack B into Openstack A, fleshed out, assuming command line:
- mount volume from BU Openstack to MOC Openstack instance
- mount BU Openstack volume to NU Openstack VM in MOC Openstack project
- in a project deploy one VM on NU, one on BU, have sit on same network

## Design assumptions and notes
- We will refer to an OpenStack instance as a Keystone, since thats the one required service (you might have only storage, or only compute in an OpenStack instance, but you will always have a keystone).
- In current version, assume user always queries all keystones, i.e. we don't keep track of a subset of keystones that he actually using; in the long term we wil want to keep track for each project what keystones.
- Each project has a **home** Keystone
- All other keystones for the project are **foreign** Keystone
- To make below efficient, we will need to cache clients for
  - for each service, you maintain the service client object, reference to keystone client object that controls it.  If it fails, we return back error, and mark it as a object that needs to be re-initialized.  When user gets error, types in passord... library will go through all timed out objects and re-initialize based on new SAML assertion. Probably organize as a hash of keystone clients based on keystone end-point, then keep list per keystone of the service end-points.  
  - in first version, we won't do this caching

## Deadlines

- Keystone Midcycle is July 15
- Openstack Summit is Oct 27 - Oct 30
- Submission deadline for the summit is July 15

## Planning and getting involved

Planning for this project will happen on this trello board: (https://trello.com/b/BQQFdyLx/os-mix-match-federation).

To get involved, please send email to (mail:moc-team-list@bu.edu) and/or join the #moc irc channel on freenode. 

###Comparisons to mercador
* Mercador doesn't want to expose any information for the user - we need to expose project for quota polling
* Mercador want's to just add a virtual region which points to the remote cluster... not enable mix and match. They are trying to avoid *all* cross region addressing through API.

## Tokyo POC : Library/API

Install two stable/kilo devstacks: call one IdP and the other SP.  Set up K2K between them.  On the IdP, use the 'moc_modified' branch of https://github.com/CCI-MOC/nova and of https://github.com/CCI-MOC/python-novaclient.

Currently, there are changes in the following areas of the code:

    nova/volume/cinder.py                              (cinder client creation)
    nova/api/openstack/compute/contrib/volumes.py      (parsing extra API option)
    nova/compute/rpcapi.py                             (sending information over messagebus)
    nova/compute/manager.py                            (getting information from messagebus)