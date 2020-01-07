---
tags:
	- CMS
	- Cryptographic
	- Message
	- Syntax
---

# Cryptographic Message Syntax (CMS)

CMS describes an encapsulation syntax for data protection. It supports digital signatures and encryption; however, the specification mainly focuses on encapsulation structure. The syntax allows multiple encapsulations; one encapsulation envelop can be nested inside another. A party can digitally sign some encapsulated data. CMS allows arbiturary attributes, such as signing time, to be signed along with the message content.

The document describes one "protection content", `ContentInfo`, representing a content along with its content type:

```
ContentInfo ::= SEQUENCE {
	contentType ContentType,
	content [0] EXPLICIT ANY DEFINED BY contentType 
}
```
- **contentType**: a unique string of integers assigned by an authority that defines the content type.
- **content**: the associated content. the type of content is determined uniquely by contentType. Content types exist for data, signed-data, enveloped-data, digested-data, encrypted-data, and authenticated-data.a
