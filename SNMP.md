---
tags:
  - SNMP
---

Simple Network Management Protocol 

A simple protocol by which anagement information for a network element may be inspected or altered by logically remote users. In the SNMP model is a collection of management "stations" and network elements. Stations execute management applications and monitor and control network elements. Elements can be hosts, gateways, terminal servers, etc and each have agents responsible for performing the network management functions requested by the stations. The network management protocol is an application protocol by which the variables of an agent's MIB may be inspected or altered.

Communication among protocol entities is accomplished by the exchange of messages, each of which is entirely and independently represented within a single UDP datagram using the basic encoding rules of ASN.1. Protocol entities receive messages at UDP port 161 on the host with which it is associated except for messages which report traps which use 162. An implementation of this protocol need not accept messages whose length exceets 484 octets.

SNMP explicitly minimizes the number and complexity of management functions "realized" by the agent itself.  
  The scope of management information is defined in the internet standard MIB or uses conventions outlined in rfc 1155
  Management information is represented according to a subset of ASN.1 for non-aggregate types in rfc 1155 (SMI)
  SNMP is generally well suited for any transport protocol but v1 specifies exchange over UDP.

SNMP models all management agent functions as alterations or inspections of variables. The strategy for monitoring the network state is accomplished primarily by polling for appropriate information. Most commands are requests either to set the value of some parameter or to retrieve such a value, only few imperative commands are supported (for example, "reboot"). The reboot command might be invoked by simply setting a parameter indicating the number of seconds until system reboot.

Architecture and relationships
  Stations and agents are application entities that communicate with each other using SNMP. 
  The peer process which implement SNMP and support the application entities are called protocol entities.

  The pairing of an SNMP agent with some arbitrary set of SNMP application entities is called an SNMP community.
    Each community is named a string of octets, that is called the community name.
  
  An SNMP message that originated by an application entitiy (that in fact belongs to the SNMP community named in the body of the message) is called an authentic SNMP message. 
    The set of rules by which an SNMP message is identified as authentic for a particular community is called an authentication scheme. An implementation of a function that identifies authentic SNMP messages according to an authentication scheme is called an authentication service.
  
In SNMPv3 privacy protocols and primatives were introduced to provide protections against disclosure of the message payload. Data integrity and origin authentication services were added to the authentication module specification.



