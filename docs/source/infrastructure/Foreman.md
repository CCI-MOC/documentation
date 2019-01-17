# Foreman

### [10/13 : Rahul] 

"error": {"message":"address family must be specified"}

This appears to be a [Foreman bug](http://projects.theforeman.org/issues/9857)

Followed the suggested workaround #14 
* Administer > Settings > Provisioning
* set ignore_puppet_facts_for_provisioning = False

