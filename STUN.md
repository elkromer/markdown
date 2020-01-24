---
tags:
  - STUN
---

# Session Traversal Utilities for NAT (STUN)

Serves as a tool for other protocols in dealing with NAT traversal. It can be used by an endpoint to determine the IP address and port allocated to it by a NAT.

One possible STUN configuration:
```
                               /-----\
                             // STUN  \\
                            |   Server  |
                             \\       //
                               \-----/




                          +--------------+             Public Internet
          ................|     NAT 2    |.......................
                          +--------------+



                          +--------------+             Private NET 2
          ................|     NAT 1    |.......................
                          +--------------+




                              /-----\
                            //  STUN \\
                           |    Client |
                            \\       //               Private NET 1
                              \-----/
```
### Binding Request

Sent from a STUN client to a STUN server. When the request arrives at the STUN server, it may have passed through one or more NATs between the client and server. As the Binding request message passes through a NAT, the NAT will modify the transport address of the packet. This new address is called the reflexive transport address. The STUN server copies the reflexive transport address of NAT 2 into an XOR-MAPPED-ADDRESS attribute in the STUN binding response and sends the binding response back to the STUN client. Now the client knows what IP and port someone on the public internet needs to use in order to build a connection to it.
