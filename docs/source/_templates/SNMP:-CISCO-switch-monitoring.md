## SNMP basics
**SNMP** stands for Simple Network Management Protocol and consists of three key components: managed devices, agents, and network-management systems (NMSs). A managed device is a node that has an SNMP agent and resides on a managed network. These devices can be routers and access servers, switches and bridges, hubs, computer hosts, or printers. An agent is a software module residing within a device. This agent translates information into a compatible format with SNMP. An NMS runs monitoring applications. They provide the bulk of processing and memory resources required for network management.


**MIB** stands for **Management Information Base** and is a collection of information which is organized hierarchically. The various pieces of information are accessed by a protocol such as SNMP. There are two types of MIBs: scalar ones and tabular ones. Scalar objects define a single object instance whereas tabular objects define multiple related object instances grouped in MIB tables.

**OIDs** or **Object Identifiers** uniquely identify manged objects in an MIB hierarchy. It can be depicted as a tree whose nodes are assigned by different organizations. Generally, an OID is a long sequence of numbers, coding the nodes, separated by dots. Top level MIB object IDs (OIDs) belong to different standard organizations. Vendors define private branches including managed objects for their own products.

SNMP basically works with the principle that network management systems send out a request and the managed devices return a response. This is implemented using one of the following four operations: Get, GetNext, Set, and Trap. SNMP messages consist of a header and a PDU (Protocol Data Unit). The headers consist of the SNMP version number and the community name. The community name is used as a sort of password to increase security in SNMP. The PDU depends on the type of message that is sent. The Get, GetNext, and Set, as well as the response PDU, consist of PDU type, Request ID, Error status, Error index and Object/Variable fields. The Trap consists of Enterprise, Agent, Agent address, Generic trap type, Specific trap code, Timestamp and Object/Value fields.[\[1\]](http://kb.paessler.com/en/topic/653-how-do-snmp-mibs-and-oids-work)

## Use net-snmp and snmpwalk
[Net-snmp](http://www.net-snmp.org/) is a suite of applications used to implement SNMP protocol, including different tools like snmpwalk. [Snmpwalk](http://www.net-snmp.org/docs/man/snmpwalk.html) is an SNMP application that uses SNMP GETNEXT requests to query a network entity for a tree of information.

Install net-snmp:
    
    Fedora/Centos: yum install net-snmp-utils
    Ubuntu: apt-get install snmp

Retrieve data through snmpwalk from the switch in NEU using the variable name "system":
    
    snmpwalk -c mars -v 2c 10.99.1.6 system
    
## Load extenal MIBs in snmpwalk
As CISCO created their own MIBs for most their devices, it's necessary to load particular MIBs to access some of the entries. Otherwise, we will get "Unknown Object Identifier" error ([Requesting an object fails with "Unknown Object Identifier" Why?](http://www.net-snmp.org/FAQ.html#How_do_I_add_a_MIB_to_the_tools_)).

    [lihua@sensu-ipmi ~]$ snmpwalk -c mars -v 2c 10.99.1.6 cpmCPUMemoryUsed
    cpmCPUMemoryUsed: Unknown Object Identifier (Sub-id not found: (top) -> cpmCPUMemoryUsed)

SNMP MIBs has dependencies on other MIBs, all MIBs must be available on the machine where the SNMP request is made, all CISCO MIBs dependencies are documented on line. The MIB can be found and downloaded from web, then should be put on the folder ```/usr/share/snmp/mibs/```. For monitoring of CISCO switches, all the following MIBs are needed.

    #Download links can be found this web page
    #(http://snmp.cloudapps.cisco.com/Support/SNMP/do/BrowseMIB.do?local=en&step=2&mibName=CISCO-PROCESS-MIB).
    1.CISCO-PROCESS-MIB
    2.CISCO-SMI
    3.CISCO-TC
    4.SNMPv2-TC

After the setup of CISCO MIBs, we can retrieve the particular variable from CISCO devices by loading the MIB.
    
    [lihua@sensu-ipmi ~]$ snmpwalk -c mars -m CISCO-PROCESS-MIB -v 2c 10.99.1.6 cpmCPUMemoryUsed
    CISCO-PROCESS-MIB::cpmCPUMemoryUsed.1 = Gauge32: 2705648

## Use ruby-snmp
Snmpwalk is good for testing the SNMP devices, but it has dependencies on the net-snmp. To make our monitoring independent of it, we created the sensu plugin using ruby-snmp to retrieve data programmingly. Ruby-snmp implements SNMP (the Simple Network Management Protocol). It is implemented in pure Ruby, so there are no dependencies on external libraries like net-snmp. You can run this library anywhere that Ruby can run [\[3\]](http://www.rubydoc.info/gems/snmp/1.2.0).

Install snmp ruby library
    
    gem install snmp

A shortcut of the code retrieving "cpmCPUTotal1minRev", "cpmCPUMemoryFree","cpmCPUMemoryUsed" from CISCO device using ruby-snmp.
```
    cpmTable_columns = ["cpmCPUTotal1minRev", "cpmCPUMemoryFree","cpmCPUMemoryUsed"]
    outputs = {}
    SNMP::Manager.open(:host => "#{config[:host]}", :community => "#{config[:community]}") do |manager|
       manager.load_module("CISCO-PROCESS-MIB")

       manager.walk(cpmTable_columns) do |row|
         cpmTable_columns.each_with_index do |column, index|
           key = [switchName,"#{column}"].join(".")
           outputs[key] = row[index].value
         end
       end
    end
```
Full code can be found in MOC sensu server [repository](https://github.com/LeonLee88/moc_sensu_server/blob/master/plugins/check_switch_cpu_memory_metrics.rb).
    
## Citations
* [1] http://kb.paessler.com/en/topic/653-how-do-snmp-mibs-and-oids-work
* [2] http://arich-notes.arich-net.com:8080/?page_id=115
* [3] http://www.rubydoc.info/gems/snmp/1.2.0