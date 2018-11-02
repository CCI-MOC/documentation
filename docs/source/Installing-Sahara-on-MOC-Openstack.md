### **These instructions are deprecated. Sahara installation is now controlled by Puppet. Check out pull requests [74](https://github.com/CCI-MOC/kilo-puppet/pull/74) and [78](https://github.com/CCI-MOC/kilo-puppet/pull/78) to find out what to add to Hiera file.**

# Installing Sahara on MOC Openstack

Instructions are based on those from https://access.redhat.com/documentation/en/red-hat-openstack-platform/8/installation-reference/chapter-11-install-the-data-processing-service  

Assume that all commands are run as root. Hopefully, a script will come soon to automate this process.  
     
The essential Sahara instructions are steps 1-13. Beyond that deals with typical extra setup needed based on what is expected in the typical MOC staging environment.

---
1. Install the necesssary packages:  
``yum install -y openstack-sahara-api openstack-sahara-engine``
2. Create the database (replacing PASSWORD with desired password):  
``mysql -e "CREATE DATABASE IF NOT EXISTS sahara; GRANT ALL ON sahara.* TO 'sahara'@'%' IDENTIFIED BY 'PASSWORD'; GRANT ALL ON sahara.* TO 'sahara'@'localhost' IDENTIFIED BY 'PASSWORD';"``  
2. Add the line ``max_allowed_packet=256M`` to /etc/my.cnf in the ``[mysqld]`` section then restart MySQL with ``systemctl restart mysqld``  
3. Add database info to sahara.conf (replacing PASSWORD with the value you set):  
 ``openstack-config --set /etc/sahara/sahara.conf database connection mysql://sahara:PASSWORD@localhost/sahara``  
4. Configure database schema:  
``sahara-db-manage --config-file /etc/sahara/sahara.conf upgrade head``
5. Set up the necessary users, services, and endpoints in keystone; be sure to ``source /root/keystonerc_admin`` before continuing:  
    * ``keystone user-create --name sahara --pass PASSWORD``  
    * ``keystone user-role-add --user sahara --role admin --tenant services``  
    * ``keystone service-create --name sahara --type data-processing --description "OpenStack Data Processing"``  
    * ``keystone endpoint-create --service sahara --publicurl 'http://HOSTNAME:8386/v1.1/%(tenant_id)s' --adminurl 'http://HOSTNAME:8386/v1.1/%(tenant_id)s' --internalurl 'http://HOSTNAME:8386/v1.1/%(tenant_id)s' --region 'MOC_Kaizen'``  
  
    _Note that in steps 6-10, the command ``openstack-config --set [FILENAME] [SECTION] [PARAMETER] [VALUE]`` is equivalent to editing the file at FILENAME and adding/changing the value of PARAMETER in SECTION to VALUE._  
  

6. Add keystone info to sahara.conf (replacing HOSTNAME with the proper hostname):  
    * ``openstack-config --set /etc/sahara/sahara.conf keystone_authtoken auth_uri http://HOSTNAME:5000/v2.0/``  
    * ``openstack-config --set /etc/sahara/sahara.conf keystone_authtoken identity_uri https://HOSTNAME:35357`` **make sure that this one is HTTPS**  
    * ``openstack-config --set /etc/sahara/sahara.conf keystone_authtoken admin_tenant_name services``  
    * ``openstack-config --set /etc/sahara/sahara.conf keystone_authtoken admin_user sahara``
    * ``openstack-config --set /etc/sahara/sahara.conf keystone_authtoken admin_password PASSWORD``(this is the password from step 5)  
7. Configure RabbitMQ in sahara.conf:  
    * ``openstack-config --set /etc/sahara/sahara.conf oslo_messaging_rabbit rabbit_use_ssl False``
    * ``openstack-config --set /etc/sahara/sahara.conf oslo_messaging_rabbit rabbit_userid $(openstack-config --get /etc/nova/nova.conf oslo_messaging_rabbit rabbit_userid)``  
    * ``openstack-config --set /etc/sahara/sahara.conf oslo_messaging_rabbit rabbit_password $(openstack-config --get /etc/nova/nova.conf oslo_messaging_rabbit rabbit_password)``  
    * ``openstack-config --set /etc/sahara/sahara.conf oslo_messaging_rabbit rabbit_virtual_host /``  
8. Enable the use of neutron in sahara.conf:  
``openstack-config --set /etc/sahara/sahara.conf DEFAULT use_neutron true`` 
9. Enable logging in sahara.conf:  
``openstack-config --set /etc/sahara/sahara.conf DEFAULT log_file /var/log/sahara/sahara.log``  
10. Enable plugins sahara.conf: (not limited to just these three)  
``openstack-config --set /etc/sahara/sahara.conf DEFAULT plugins vanilla,hdp,cdh``  
11. Make Heat not have timeout issues:  
``openstack-config --set /etc/sahara/sahara.conf DEFAULT heat_enable_wait_condition false``  
11. Add the line ``-A INPUT -p tcp -m multiport --dports 8386 -j ACCEPT`` to the file ``/etc/sysconfig/iptables``, and then restart the iptables service with ``systemctl restart iptables.service``  
12. Start the sahara services, and enable them to start at boot:  
    * ``systemctl start openstack-sahara-api``  
    * ``systemctl start openstack-sahara-engine``  
    * ``systemctl enable openstack-sahara-api``  
    * ``systemctl enable openstack-sahara-engine``  

    **The following steps deal with the configuration of Heat. If your Openstack installation is based on the MOC puppet scripts for staging environments, Heat probably isn't fully configured yet. If at any time during steps 15-21 you experience unexpected behavior, switch over to the instructions at https://access.redhat.com/documentation/en/red-hat-openstack-platform/8/installation-reference/chapter-9-install-the-orchestration-service ... Additionally, note that OpenStack versions Liberty and prior can function without heat, using the deprecated "direct" orchestration service. To bypass heat, set infrastructure_engine=direct in DEFAULT section of sahara.conf**   

14. ``yum list *heat*`` and make sure that Heat was installed  
15. Check if Heat has database with ``mysql -e "SHOW DATABASES LIKE 'heat'"`` 
16. Check if heat.conf has SQL connection with ``cat /etc/heat/heat.conf | grep mysql``  
17. Check if Heat has a user with ``keystone user-list``  
18. Run ``keystone user-role-add --user heat --role admin --tenant services`` and hopefully you will find out that the user already has the role.  
19. Check ``keystone service-list`` for "heat" and "heat-cfn" services  
20. Make sure Heat has endpoints in keystone with ``keystone endpoint-list | grep PORT`` with PORT being 8000 and 8004  
    **From step 22 onward, you are no longer checking existing configuration, but instead adding new functionalities and configurations.**
21. ``heat-manage db_sync`` to build Heat database.  
22. Find Keystone admin token with ``cat /etc/keystone/keystone.conf | grep admin_token``  
23. Create Heat domain: ``openstack --os-token ADMIN_TOKEN --os-url=https://HOSTNAME:5000/v3 --os-identity-api-version=3 domain create heat --description "Owns users and projects created by heat"``... this returns a domain ID which you need for next steps.  
24. Create admin user of heat domain: ``openstack --os-token ADMIN_TOKEN --os-url=https://HOSTNAME:5000/v3 --os-identity-api-version=3 user create heat_domain_admin --password PASSWORD --domain DOMAIN_ID --description "Manage users and projects created by heat"``...this return a user ID which you need for the next step.  
25. Grant admin role: ``openstack --os-token ADMIN_TOKEN --os-url=https://HOSTNAME:5000/v3 --os-identity-api-version=3 role add --user USER_ID --domain DOMAIN_ID admin``  
26. Add the info you created to heat.conf with ``openstack-config --set /etc/heat/heat.conf DEFAULT``...  
    * ``stack_domain_admin_password PASSWORD``  
    * ``stack_domain_admin USERNAME``  
    * ``stack_user_domain_name DOMAIN``  
27. Restart services with ``systemctl restart``...  
    * ``openstack-heat-api``    
    * ``openstack-heat-engine``  
 





