Most instructions for debugging Horizon assume that you are running in Devstack, or in some other manual install, where you have a checkout of the source code available.  When Horizon has been installed system-wide (e.g. with Fuel or Foreman), you don't have this.

So, the idea is to fake it, using a lot of symlinks.

    mkdir HORIZON_TEST
    cd HORIZON_TEST
    ln -s /usr/share/openstack-dashboard/static
    ln -s /usr/share/openstack-dashboard/manage.py
    mkdir openstack_dashboard
    cd openstack_dashboard
    ln -s /usr/share/openstack-dashboard/openstack_dashboard/*
    rm static
    ln -s /usr/share/openstack-dashboard/static
    cd ..
    mkdir horizon
    cd horizon
    ln -s /usr/lib/python2.7/site-packages/horizon/*
    rm static
    ln -s /usr/share/openstack-dashboard/static
    cd ..

Then, in that HORIZON_TEST folder, you can run

    python -m pdb manage.py runserver

This launches the Horizon dashboard on port 8080.  None of the
CSS, images, or javascript will work correctly, but the site is
still (somewhat) usable.

Then by inserting ``pdb`` traces in the right locations, you can debug Horizon with pdb.
