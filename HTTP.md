---
tags:
	- HTTP
	- Authentication
---

# Hypertext Transfer Protocol (HTTP)
* HTTP [~~[HTTP/1.0](https://tools.ietf.org/html/rfc1945)~~, ~~[RFC 2068](https://tools.ietf.org/html/rfc2068)~~, [RFC 2616](https://tools.ietf.org/html/rfc2616)]
* HTTP Authentication Architecture [[RFC 7235](https://tools.ietf.org/html/rfc7235)]
* HTTP Authentication: Basic Access Authentication [~~[RFC 2617](https://tools.ietf.org/html/rfc2617)~~ -> [RFC 7617](https://tools.ietf.org/html/rfc7617)]
* HTTP Authentication: Digest Access Authentication [~~[RFC 2069](https://tools.ietf.org/html/rfc2069)~~ -> ~~[RFC 2617](https://tools.ietf.org/html/rfc2617)~~ -> [RFC 7616](https://tools.ietf.org/html/rfc7616)]

### Transfer Encodings

In HTTP, the only unsafe characteristic of message-bodies is the difficulty in determining the exact body length, or the desire to encrypt data over a shared transport.

Transfer coding values are used to indicate an encoding transformation that has been applied to an entity body in order to ensure "safe transport" through the network. **Not like content encoding, tranfser encoding is a property of the message, not of the original entity**. 

**Chunked encoding**

Modifies the body of a message in order to transfer it as a series of chunks, each with its own size indicator, followed by an optional footer. It is ended by a zero-sized chunk followed by the footer which is terminated by an empty line.