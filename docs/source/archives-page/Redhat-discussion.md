##Questions:

To prep for friday, wanted to outline the key areas of discussion, some of which we may be able to address ahead of time. We should possibly try to nail as many of them as we can ahead of time by email, and phone and determine what we are going to work with intensely during the day, demos, and white board conversations. 


 1. Support for current systems at MGHPCC, HPC infrastructure, and applications 
 1. Infrastructure we are provisioning
 1. Use of production storage services at MGHPCC
 1. Operational requirement
 1. Foreman deployment
 1. Discussion HaaS
 1. Discussion UI
 1. Other

#### Current/HPC infrastructure/applications
We have today access to hardware that was originally purchased for HPC
like workloads, it has IB support and GPUs, both of which we are
currently ignoring.  If we can demonstrate value for HPC
workloads it would be great.  Also, there is substantial capacity in the MGHPCC that
is provisioned with IB and GPUs, that we would like to have
transferred to MOC over time. 

 - status of GPU support KVM, do we need to use Xen?
 - IB support for Neutron (we have heard isolation does not work properly)
 - IB support when using for storage fabric
 - exposing (multiple) existing Lustre file system from infrastructure, i.e., BU and NEU both have their own Lustre file systems in the infrastructure, would be good if clients running on open-stack could use
      - sharing identity/authorization 
 - exposing distributed file system running on the infrastructure, e.g., virtual Lustre
 - status of support for Lustre plugin for basic storage (seems like just research)
 - starcluster 


#### SGI Hardware 
We have commitments for various hardware resources.  This includes 8
racks of SGI Cloud Rack C2 hardware, and networking commitments from
a variety of providers.   We should work together on defining what
commits we will ask for, and architecting the first deployment. 

 - thoughts about high-speed network internally to enable Hadoop on
 remote/distribute storage
 - what should we be using for high-speed networking internally, will
 IB work for storage?  Lustre support for IB?
 - cheap disks from ORNL for ephemeral
 - thoughts on production storage, do we want to push hard for high-end
 storage vendor..., versus distributed storage like Gluster integrated
 - discuss elastic pools of OpenStack, and how we de-couple storage
 - how much storage do we want directly attached

#### Production Storage

We have both a production luster storage service attached to the MRI
system, and a large Gluster deployment by Harvard in the MGhPCC. Would
be really good if we could use one of those options for
*production* storage.

 - again, issues on exposing Lustre to guests, identity..., networking
 - The Harvard guys are super worried about giving us access to their Gluster service since it is *real* production, and they are worried that pointing our OpenStack service at it could compromise it. 
      - Can we build a set of benchmarks to demonstrate won't  destabilize
	  - quotas
	  - is each cinder volume a separate file, can we snapshot, de-duplicate, archive at FS level?


#### Operational requirement
We are coming up with a more detailed project plan.  It looks like we
will have a pretty much full time ops person from Harvard, Dan,
possible support from ORNL.

 - How much of our own Operations team should we plan on building for  a production service
 - What kind of involvement and what commitment can RH provide from ops perspective.
 -  discuss what we should be using for monitoring (e.g. Nagios),
 - how mature is Ceilomenter, what Billing options do you have experience with.  Won't be critical in first   year, but good to know
 - Scalability of puppet, given a single master... what about ansible?  From Rackspace talk, looks like they blast VMs for upgrade, rather than using puppet, this seems like important space
 - ORNL is still in discussion about very early doing federation, at the very least for DR, planning around that.
 - we need to better understand update strategies being used with open stack, so far we have been blowing things away and re-starting,  
      - how do you migrate VM from old to new deployment... networking?


#### Foreman deployment
We would prefer to move away from Fuel and to Forman.  Key problems/questions we had in past where:

  1. savannah support; kept tromping over configuration,
  2. log visibility
  3. upgrade model
  4. small deployments: e.g., using virtual rather than physical hosts for management

We would like to see roadmap, and current version.  We should come up with plan for testing new versions. 

#### Discussion HaaS

A key value of the MOC will be that we can stand up experimental services alongside production services.

  - We should probably review with you the current HaaS model we have been building, and get feedback.
  - discuss integration of foreman into the HaaS head node
  - experience with Canonical MaaS
  - discuss elastic deployment of OpenStack, i.e., grows/shrinks as
  demand on different clusters change

#### Discussion UI 

We did a quick prototype of a new UI that: 1) exposes marketplace model, and 2) provides end users with a much simpler model.  We are integrating into Horizon, would like to demo, and discuss strategy.
- Stable dev environment for Horizon , are there any dependencies on OpenStack which might affect it, for example on upgrading the OpenStack would the local setting of Horizon be affected.
- Mock UI wireframes showcasing the workflow diagram for our future needs.

#### Other 
 - deployment/support of openshift for MOC users? 
 - what else?
