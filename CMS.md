---
tags:
	- CMS
	- Cryptographic
	- Message
	- Syntax
---

# Cryptographic Message Syntax (CMS)
> A general syntax for data that may have cryptography applied to it.

[PKCS #7](https://tools.ietf.org/html/rfc2315) `->` [CMS1 (RFC 2630)](https://tools.ietf.org/html/rfc2630) `->` [CMS2 (RFC 3369)](https://tools.ietf.org/html/rfc3369) `->` [CMS3 (RFC 3852)](https://tools.ietf.org/html/rfc3852) `->` [CMS4 (RFC 5652)](https://tools.ietf.org/html/rfc5652)

CMS describes a syntax for representing data that may have cryptography applied to it, such as digital signatures, digital envelopes, attributes, signing time, and contents of a message. The syntax allows multiple encapsulations contained within it.

### History

The original specification for CMS Version 1.5 was [PKCS #7](https://tools.ietf.org/html/rfc2315). RFC 2630 was the first version of CMS on the IETF Standards Track in which minor changes were made to accomodate "version 1 attribute certificate transfer" and support "algorithm-independent key management".  RFC 3369 obsoletes RFC 2630 and defines an extension mechanism to support new key management schemes without further changes to the CMS. RFC 3852 defines a similar extension mechanism to support additional certificate formats. RFC 5652 obsoletes RFC 3852 "to advance the CMS along the standards maturity ladder", clarify the proper handling of SignedData protected content type when more than one digital signature is present, and corrected errors in RFC 3852.

## Public Key Cryptography Standards #7
> PKCS is a group of cryptography standards devised and published by RSA Security LLC starting in the early 1990s

Defines six content types: data, signed data, enveloped data, singed-and-enveloped data, digested data, and encrypted data. The two classes of content types are:
* **Base** no cryptographic enhancements.
	+ `data`
* **Enhanced** content of some type with cryptographic enhancements
	+ `signed data`, `enveloped data`, `digested data`, `encrypted data`

The content types in the enhanced class employ encapsulation. For example, the `enveloped data` content type can contain encrypted, signed data content which can contain data. This gives rise to the term `outer` content to describe the data containing the enhancements and `inner` content to describe the data being enhanced.

The general syntax for content exchanged between entities according to PKCS #7 associates a content type with content.

```
   ContentInfo ::= SEQUENCE {
     contentType ContentType,
     content
       [0] EXPLICIT ANY DEFINED BY contentType OPTIONAL }
```

The `contentType` defines the type of content. It can be a unique string of integers assigned by the authority that defines the content type. 

The `content` is the content. The type of content can be determined uniquely by the contentType.

When `ContentInfo` is the inner content of signed-data, signed-and-enveloped data, or digested data (i.e. when the content is the data being enhanced), a message-digest algorithm is applied to the contents of octets of the `DER` encoding of the content field. When `ContentInfo` is the inner content of enveloped-data or signed-and-enveloped data, a content-encryption algorithm is applied to the contents of octets of a definite-length `BER` encoding of the content field.

```
   EncryptedData ::= SEQUENCE {
     version Version,
     encryptedContentInfo EncryptedContentInfo }

   EncryptedContentInfo ::= SEQUENCE {
     contentType ContentType,
     contentEncryptionAlgorithm
       ContentEncryptionAlgorithmIdentifier,
     encryptedContent
       [0] IMPLICIT EncryptedContent OPTIONAL }

   DigestedData ::= SEQUENCE {
     version Version,
     digestAlgorithm DigestAlgorithmIdentifier,
     contentInfo ContentInfo,
     digest Digest }

   Digest ::= OCTET STRING	   
```