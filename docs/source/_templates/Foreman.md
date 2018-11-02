* [10/13 : Rahul] 

1. "error": {"message":"address family must be specified"}

This appears to be a Foreman bug according to http://projects.theforeman.org/issues/9857. 
Followed the suggested workaround #14... 

Administer > Settings > Provisioning
[change] ignore_puppet_facts_for_provisioning = False
