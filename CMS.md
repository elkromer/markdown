---
tags:
	- CMS
	- Cryptographic
	- Message
	- Syntax
---

# Cryptographic Message Syntax (CMS)

CMS describes an encapsulation syntax for data protection. It supports digital signatures and encryption; however, the specification mainly focuses on encapsulation structure. [RFC 3852](https://tools.ietf.org/html/rfc3852#section-1) defines many ASN.1 types used in the signing, digesting, authenticating, or encrypting of arbitrary message content.

The syntax allows multiple encapsulations; one encapsulation envelop can be nested inside another. A party can digitally sign some encapsulated data. CMS allows arbiturary attributes, such as signing time, to be signed along with the message content.

