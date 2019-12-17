---
tags:
  - IPv4
---

Internet Protocol

Provides for transmitting and fragmentation of blocks of data from sources to destinations. There are no mechanisms to augment end-to-end data reliability, flow control, sequencing, or other services commonly found in host-to-host protocols.

Internet protocol implements two basic functions: addressing and fragmentation. IP modules use the addresses carried in the internet header to transmit datagrams toward their destinations. An internet module resides in each host engaged in internet communication and in each gateway that interconnects networks. 

The internet protocol treats each internet datagram as an independent entity unrelated to any other internet datagram. There are no connections or logical circuits. 

Four key mechanisms in providing internet protocol's service.
Type of Service: Quality of service desired. Generalized parameters that characterize the service choices provided in the networks that make up the internet. Used by gateways to select the actual transmission parameters for a particular network, the network to be used for the next hop, or the next gateway when routing a datagram.
Time to Live: Indication of the upper bound on the lifetime of an internet datagram. Set by the sender and reduced at points along the route. If TTL reaches 0 before it reaches the destination the datagram is destroyed. A self-destruct limit. 
Options: Provides control functions needed in some situations but unneccessary for most common communications. E.g. timestamps, security, special routing.
Header Checksum: Provies a verification that the information used in processing internet datagram has been transmitted correctly. The data may contain errors. If the checksum fails the datagram is discarded at once by the entity which detects the error.

It is the task of higher level protocols to make the mapping from names to addresses. The internet module maps internet addresses to local net addresses. It is the ask of lower level procedures to make the mapping form local net addresses to routes.



