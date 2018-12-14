## Testing for ansible-openstack all-in-one deployment

For this install, I am following the doc here: https://github.com/openstack/openstack-ansible/blob/master/doc/source/developer-docs/quickstart-aio.rst

and running the appropriate scripts.

Additional set-up:
Installed ubuntu 14.04 server with 8Gb ram, 100Gb disk, and 4 VCPUs. Min for all-in-one (AIO) requires 60Gb free disk space and 8Gb ram, because AIO sets up a container for each service.

Packages installed, once server is online: git, vim, python, python-dev, gcc, xenstore-utils.