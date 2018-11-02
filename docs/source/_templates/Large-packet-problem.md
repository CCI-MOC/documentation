### The problem we are currently having on the kilo deployment is sending large packets.

#### Symptoms:
* ssh times out after first request, outside of cluster
  * ssh only works from the controller node hosting the router to the vm
* Large packets (greater then 1480 bytes, 1472 + 8 byte ping header) get an icmp_timeout
* Large packets can flow between compute and controller node, but not from either to the internet, or vice versa