---
tags:
	- 3DS 1.0
	- 3D
	- Secure
---

# Three-Domain Secure 

An XML-based protocol designed to be an additional security layer for online credit and debit card transactions. The name refers to the number of hosts involved.

EMV 3DS is a messaging protocol developed by EMVCo to enable consumers to authenticate themselves with their card issuer when making card-not-present (`CNP`) e-commerce purchases. Helps to add an additional layer of security for unauthorized `CNP` transactions and protects the merchant from `CNP` exposure to fraud.

* Supports specific flows for types of devices


### Background

The past few years have seen an increase in the use of mobile devices. More people are buying e-commerce items from their phone. 

>There is a need to improve authentication security in mobile-based apps.

## Overview

The basic concept is to tie the financial authorization process with online authentication. The three domains in the model are
```
├── Acquirer domain
├── Issuer domain
├── Interoperability domain (Payment Systems)
```

The protocol uses `XML` messages sent over `TLS` connections **with client authentication**. 

**Example**

A transaction using *Verified-by-Visa* or MasterCard *SecureCode* will initiate a redirection to the website of the card-issuing bank to authorize the transaction.



## Abbreviations

![Abbreviations](/resources/3ds-abbreviations.png)