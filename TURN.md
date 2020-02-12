---
tags:
  - TURN
---

# Traversal Using Relays around NAT (TURN)
> Relay Extensions to STUN

If a host is located behind a NAT, then in certain situations it can be impossible for that host to communicate directly with other peers which may also be behind NATs. This specification defines a protocol that allows a host behind a NAT (called a TURN client) to request that another host (called the TURN server) act as a relay. The client can arrange for the server to relay packets to and from certain other hosts and control aspects of how the relaying is done.

Traditionally, hosts can use "hole punching" techniques ([RFC 5128](https://tools.ietf.org/html/rfc5128)) to attempt to discover a communication path. Hole punching will fail if both hosts are behind NATs that are not well-behaved. 

TURN was designed to be used as a part of the ICE approach to NAT traversal, though it can be used without ICE.

**Typcial Deployment:**
```
                                        Peer A
                                        Server-Reflexive    +---------+
                                        Transport Address   |         |
                                        192.0.2.150:32102   |         |
                                            |              /|         |
                          TURN              |            / ^|  Peer A |
    Client's              Server            |           /  ||         |
    Host Transport        Transport         |         //   ||         |
    Address               Address           |       //     |+---------+
   10.1.1.2:49721       192.0.2.15:3478     |+-+  //     Peer A
            |               |               ||N| /       Host Transport
            |   +-+         |               ||A|/        Address
            |   | |         |               v|T|     192.168.100.2:49582
            |   | |         |               /+-+
 +---------+|   | |         |+---------+   /              +---------+
 |         ||   |N|         ||         | //               |         |
 | TURN    |v   | |         v| TURN    |/                 |         |
 | Client  |----|A|----------| Server  |------------------|  Peer B |
 |         |    | |^         |         |^                ^|         |
 |         |    |T||         |         ||                ||         |
 +---------+    | ||         +---------+|                |+---------+
                | ||                    |                |
                | ||                    |                |
                +-+|                    |                |
                   |                    |                |
                   |                    |                |
             Client's                   |            Peer B
             Server-Reflexive    Relayed             Transport
             Transport Address   Transport Address   Address
             192.0.2.1:7000      192.0.2.15:50000     192.0.2.210:49191

                                 Figure 1
```							 
```							 
  TURN                                 TURN           Peer          Peer
  client                               server          A             B
    |-- Allocate request --------------->|             |             |
    |                                    |             |             |
    |<--------------- Allocate failure --|             |             |
    |                 (401 Unauthorized) |             |             |
    |                                    |             |             |
    |-- Allocate request --------------->|             |             |
    |                                    |             |             |
    |<---------- Allocate success resp --|             |             |
    |            (192.0.2.15:50000)      |             |             |
    //                                   //            //            //
    |                                    |             |             |
    |-- Refresh request ---------------->|             |             |
    |                                    |             |             |
    |<----------- Refresh success resp --|             |             |
    |                                    |             |             |

                                 Figure 2								 
```
```
  TURN                                 TURN           Peer          Peer
  client                               server          A             B
    |                                    |             |             |
    |-- CreatePermission req (Peer A) -->|             |             |
    |<-- CreatePermission success resp --|             |             |
    |                                    |             |             |
    |--- Send ind (Peer A)-------------->|             |             |
    |                                    |=== data ===>|             |
    |                                    |             |             |
    |                                    |<== data ====|             |
    |<-------------- Data ind (Peer A) --|             |             |
    |                                    |             |             |
    |                                    |             |             |
    |--- Send ind (Peer B)-------------->|             |             |
    |                                    | dropped     |             |
    |                                    |             |             |
    |                                    |<== data ==================|
    |                            dropped |             |             |
    |                                    |             |             |

                                 Figure 3
```

## High Level Overview

The client uses `TURN` commands to create and manipulate an `ALLOCATION` on the server.  An allocation is a data structure on the server. This data structure contains, amongst other things, the `RELAYED TRANSPORT ADDRESS` for the allocation.  The relayed transport address is the transport address on the server that peers can use to have the server relay data to the client.  An `ALLOCATION` is uniquely identified by its relayed transport address.

Once an allocation is created, the client can send application data to the server along with an indication of to which peer the data is to be sent, and the server will relay this data to the appropriate peer.  The client sends the application data to the server inside a TURN message; at the server, the data is extracted from the TURN message and sent to the peer in a UDP datagram.  

In the reverse direction, a peer can send application data in a UDP datagram to the relayed transport address for the allocation; the server will then encapsulate this data inside a TURN message and send it to the client along with an indication of which peer sent the data.  Since the TURN message always contains an indication of which peer the client is communicating with, the client can use a single allocation to communicate with multiple peers.
   
Once a relayed transport address is allocated, a client must keep the
allocation alive.  To do this, the client periodically sends a
`REFRESH` request to the server.  TURN deliberately uses a different
method (`REFRESH` rather than `ALLOCATION`) for refreshes to ensure that the client is informed if the allocation vanishes for some reason.
   
Each allocation on the server belongs to a single client and has
exactly one relayed transport address that is used only by that
allocation.  Thus, when a packet arrives at a relayed transport
address on the server, the server knows for which client the data is
intended.