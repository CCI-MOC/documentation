We're moving towards using Hiera to store parameters for our puppet manifests, instead of foreman's overrides. Here's where we are:

hiera looks for it's config file at `/etc/hiera.yaml` when running from the command line, and `/etc/puppet/hiera.yaml` when invoked by puppet. The latter didn't exist on our system, so it's now a symlink to the former.

The file lists a series of places it will search for values. For the ongoing work of moving our openstack stuff to heira, The data is going in the environment-specific source, which is at `/var/lib/hiera/production.yaml`.

`moc-config::params` is will do fairly little (and probably be removed at some point); just assign the hiera values to the appropriate variables, rather than having to find each instance of in the code and change it.