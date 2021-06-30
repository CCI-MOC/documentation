# OCT Nodes

The MOC has access to nodes in the OCT racks.

We have 2 IPMI VLANs (911, 912), and a VLAN (207) for switch management. Please see https://docs.massopen.cloud/en/latest/clusters/kaizen2/Kaizen-2.html for more detailed network breakdown.

Details about OCT nodes are here: https://docs.google.com/spreadsheets/d/1V4_24MEQvpQaEIWOY9CT3zMeu_7llBagkYRxAIsJ1a4/edit#gid=26142192


## OCT3

This rack hosts node to be used as computes for operate first cluster. The node's IPMI is on VLAN 912.

Available nodes are from OCT3-00 to OCT3-31 (total of 31 nodes).

Each OCT3 node has a single connection to the OCT3 10G Switch.

## OCT10

This rack has the newer dell hardware to be used as controllers for the operate first cluster. The node's IPMI is on VLAN 912.

Nodes have dual 25G connections to a DellZ9100-ON. See the google sheet on top for details about switchports.

## OCT4

This rack has 17 nodes available to be used for ESI. These nodes' IPMI is on VLAN 911.

Each node has a single 10G connection to the OCT4 10G Switch.

There are also 3 intel nodes racked here, but those are not to be used under ESI. Their IPMI is also on VLAN 911.
