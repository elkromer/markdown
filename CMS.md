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

