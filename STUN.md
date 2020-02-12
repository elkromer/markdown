---
tags:
  - STUN
---

# Session Traversal Utilities for NAT (STUN)
> A tool for dealing with NATs

[RFC 3489](https://tools.ietf.org/html/rfc3489)`->`[RFC 5389](https://tools.ietf.org/html/rfc5389)

It can be used by an endpoint to determine the IP address and port allocated to it by a NAT that corresponds to its private IP address and port. It can be used by an endpoint to determine the IP address and port allocated to it by a NAT. It can also be used to check the connectivity between two endpoints. 

STUN was originally defined in [RFC 3489](https://tools.ietf.org/html/rfc3489), "classic STUN" which represented itself as a complete solution to the NAT traversal problem. Experience since that publication has found that classic STUN simply does not work sufficiently well to be widely deployed. The address and port learned through classic STUN are sometimes usable and sometimes not.  

**One possible STUN deployment**:
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
### General Overview

STUN is a client-server protocol that supports two types of transactions. One is a request/response transaction in which the client sends a request and the server responds. The second is an indication transaction in which either agent sends an indication that generates no response.

The document defines a single method called `Binding` which can be used either in req/res transactions or indication transactions. They leave it up to future documents to define other methods.

### Binding Request

When used in a req/res transaction the `Binding` method can be used to determine the particular "binding" a NAT has allocated to a STUN client. The `Binding` method can also be used to keep the "binding" alive.

In the req/res transaction, a `Binding` request is sent from a STUN client to a STUN server. When the request arrives at the STUN server, it may have passed through one or more NATs between the client and server. As the Binding request message passes through a NAT, the NAT will modify the transport address of the packet. This new address is called the reflexive transport address. The STUN server copies the reflexive transport address of NAT 2 into an XOR-MAPPED-ADDRESS attribute in the STUN binding response and sends the binding response back to the STUN client. Now the client knows what IP and port someone on the public internet needs to use in order to build a connection to it.
