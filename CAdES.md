---
tags:
	- CAdES
	- CMS
	- Advanced
	- Digital
	- Signatures
---

# CMS Advanced Electronic Signatures

[RFC 5126](https://tools.ietf.org/html/rfc5126.html) defines a specification for advanced digital signatures (intended for use in various types of transactions where long-term validity of electronic signatures is important). The document defines a number of electronic signature formats. It builds on the [Cryptographic Message Syntax RFC](https://tools.ietf.org/html/rfc3852) and [Internet X.509 Public Key Infrastructure RFC](https://tools.ietf.org/html/rfc3280).

### **electronic signature**

Data in electronic form that is attached to or logically associated with other electronic data and that serves as a method of authentication.

### **extended electronic signatures**

Electronic signatures enhanced by complementing the baseline requirements with additional data, such as time-stamp tokens and certificate revocation data, to address commonly recongnized threats.

## CAdES-BES

CAdES Basic Electronic Signatures satisfy the legal requirements for electronic signatures as defined in the European Directive on Electronic Signatures. It provides basic authentication and integrity protection. A CAdES-BES contains:
- The signed user data
- A collection of mandatory and optionalluy signed attributes
- The digital signature value computer on the user data and signed attributes

The mandatory signed attributes are:
- [Content-type](https://tools.ietf.org/html/rfc3852) (section 5.7.1)
- [Message-digest](https://tools.ietf.org/html/rfc3852) (section 5.7.2)
- [ESS signing-certificate-v2](https://tools.ietf.org/html/rfc5126.html#section-5.7.3) (section 5.7.3)

```
                +------Elect.Signature (CAdES-BES)------+
                |+----------------------------------- + |
                ||+---------+ +----------+            | |
                |||Signer's | |  Signed  |  Digital   | |
                |||Document | |Attributes| Signature  | |
                |||         | |          |            | |
                ||+---------+ +----------+            | |
                |+------------------------------------+ |
                +---------------------------------------+

                  Figure 1: Illustration of a CAdES-BES
```