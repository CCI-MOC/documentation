# Installing the OpenShift command line tools

The `oc` (or `kubectl`) command line tools are used to access Openshift
through the command line.

You can download the latest version of the OpenShift client tools from <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>:

- [Linux](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz)
- [MacOS](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-mac.tar.gz)
- [Windows](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-windows.zip)

Now, extract the downloaded file. On Linux, run:

```shell
tar –xvf openshift-client-linux.tar.gz
```

This will produce two commands, `oc` and `kubectl`. Place these somewhere in your `$PATH`. If you want the commands to be available to multiple users on a system, consider placing them in `/usr/local/bin`:

```shell
mv oc kubectl /usr/local/bin
```

If you are installing them just for yourself, you can install them in `$HOME/bin`:

```shell
mkdir -p $HOME/bin
mv oc kubectl $HOME/bin
```

Lastly, ensure that the directory in which you have placed the commands is in your `$PATH`. Typically, your system will already be configured to find commands in `/usr/local/bin`.  You may need to add `$HOME/bin` to your path by editing your `.bashrc` file and adding:

```shell
export PATH=$HOME/bin:$PATH
```
