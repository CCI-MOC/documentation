* Started with a purely Fuel Web install
* This works inconsistently with not very helpful debug information
  * Specifically, we were getting timeouts on the machines which would disconnect and later reconnect
  * Our solution was to simply kill the environment, reboot the machines, and rerun
* While this worked, Fuel Web doesn't support use of Quantum or Swift, which we will want
* This is only supported through the Fuel command line tool
  * Our hope is to write a script to simply inject our known paramaters into these files (config.yaml, astute.yaml, and puppet.conf)
  * However, there are too many undocumented (that we've found) parameters with non-obvious usage
* In an attempt to get some understanding, we ran the Fuel Web installer as before, hoping we could examine how it configured these files
  * However, the Fuel Web doesn't seem to use these files, as they were unmodified after successfully installing

* Direction
  * Ping Mirantis about how to use their tools
  * Gain better insight into how these 'blackbox' tools work
  * Make decision if we even want these tools as opposed to writing our own scripts