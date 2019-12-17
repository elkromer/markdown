---
tags:
  - IGMP
---

Internet Group Management Protocol
  Hx: Host Extensions for IP Multicasting -> IGMP v2 -> IGMP v3 -> Multicast listener discovery

Used by IP hosts to report their multicast group memberships to routers. IP multicasting is the transmission of an IP datagram to a "host group", a set of zero or more hosts identified by a single IP destination address. A multicast datagram is delivered to all members of its destination host group. 

Group membership is dynamic - hosts may join an leave groups at any time. There is no restriction on the number of members or location of members in a host group. A host may be in more than one group at a time. A host does not need to be a member of a group to send datagrams to it. 

Internetwork forwarding of IP multicast datagrams is handled by multicast routers which may be co-resident or separate from internet gateways. If the datagram has a TTL greater than 1 the multicast router attached to the local network takes responsibility for forwarding it towards all other networks that have members of the destination group. Delivery is completed by transmitting the datagram as a local multicast.

