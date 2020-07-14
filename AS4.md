---
tags:
	- EDI
	- AS4
---

# Applicability Statement 4 (AS4)

> AS4 is an open standard for the secure and payload-agnostic exchange of business-to-business documents using web services. It's a web-services update to AS2, built largely on the OASIS ebMS 3.0 framework.

Its lightweight nature makes it a relatively low-cost communications standard to implement for organizations with minimal IT infrastructure. AS4 is a SOAP-based web API layered over HTTP from a technical viewpoint and can be EDI from a business viewpoint.

* More compatible with standard environments than AS2
* Can transport any type of payload
* Provides for rerouting messages and correlating them with past or future messages
* Large file & compression support

### Where is it used

AS4 is increasingly being adopted in the European government and energy sectors and is also gaining increasing popularity and use among all manner of organizations modernizing their internal technologies as well as extending their standards-based internal integrations.

### What is ebXML?

Electronic Business using eXtensible Markup Language. A family of standards sponsored by OASIS and UN/CEFACT. The original project envisioned five layers of data specification, including XML standards for:
* Business processes
* Collaboration protocol agreements
* Core data components
* Messaging
* Registries and repositories

[ISO-15000] contains the five specifications:
* Collaborative Partner Profile Agreement (ebCPP)
* Messaging Service Specification (ebMS)
* Registry Information Model (ebRIM)
* Registry Services Specification (ebRS)
* Core Components Specification (CCS)

Any questions about SOAP or XML in AS4? Look at ebMS.

## What is AS4?

In general it is a federation of conformance profiles of the [OASIS ebMS V3](http://docs.oasis-open.org/ebxml-msg/ebms/v3.0/profiles/AS4-profile/v1.0/os/AS4-profile-v1.0-os.html) specification. When talking about AS4 more specifically, it usually refers to an implementation of a particular conformance profile. 

- **Conformance Profiles**: The different ways a product can conform to a standard based on specific ways to implement this standard. The define a precise subset of features to be supported.
- **Usage Profiles**: How a standard should be used by a community of users in order to ensure best compatibility with business practices and interoperability.

The main benefits of AS4 compared to AS2 are
- compatibility with web services standards
- message pulling capabilities
- a built-in receipt mechanism

AS4 is defined as a combination of:

- Three primary conformance profiles that define three subsets of ebMS V3 features, at least one of which is to be supported by an AS4 implementation.
	+ **AS4 ebHandler:** Supports sending and receiving roles, for each roll both message pushing and pulling.
	+ **AS4 Light Client:** Supports sending and receiving roles, but only message pushing for `Sending` and message pulling for `Receving`. It does not support incoming HTTP requests and may have no fixed IP.
	+ **AS4 Minimal Client:** Similar to Light Client, does not support the push transport channel for `Receiving` and has no HTTP server capabilities. It omits all but a minimal set of features.
- An optional complementary conformance profile that specifies how to use AS4 endpoints with ebMS V3 intermediaries. 
- An AS4 Usage Profile that defines how to use an AS4-compliant implementation in order to acheive similar functions specified in AS2.

### Functional Aspects of AS4 ebHandler

- **ebMS MEP (Message Exchange Patterns):** One-way push and one-way pull must be supported in both the `Initiaing` and `Responding` partner. Two-way MEPs are optional.
	
	Regardless of which MEP, the sending of a `eb:Receipt` message must be supported.

	+ One-way/Push both "response" and "callback" reply patterns must be supported.
	+ One-way/Pull "callback" must be supported, at least when an `eb:Receipt` is piggybacked on an `eb:PullRequest`
- **Reliability:** The sending ebHandler must be able to notify the application of a lack of reception of an `eb:Receipt`
- **Security:** The following security features:
	+ Username/password token, digital signatures, and encryption
	+ Content-only transforms
	+ Security of attachments
	+ Message authorization at P-Mode level [7.10 in ebMS3Core](http://docs.oasis-open.org/ebxml-msg/ebms/v3.0/core/os/ebms_core-3.0-spec-os.pdf) Authorization of the Pull signal.
	+ Transport-level secure protocols such as SSL or TLS

	Two authorization optionsmust be supported in the `Receiving` role and at least one of them in the `Sending` role.

	+ Use of the WSS security header targeted to the "ebms" actor with the `wsse:UsernameToken`. See 7.10 above.
	+ Use of a regular wsse security header and no additional wsse security header targeted to "ebms" (XMLDsig or X509 for authentication). The MSH must be able to use the credential present in the security header for Pull authorization.

- **Error generation and reporting:** MSH must be able to report errors either sending it as a separate request or sending the error on the back channel of the underlying protocol. It must be able to report it to a third-party address and to the application. There are exceptions to this rule for security headers. See the spec.

- **Message Partition Channels (MPC):** Additional message channels must be supported so that selective pulling by a partner is possible. 

- **Message Packaging:** In both `Sending` and `Receiving` roles the following features must be supported:
	+ Attachments
	+ Message Properties
	+ Support for processing messages containing `eb:SignalMessage` and `eb:UserMessage`. This can happen.

- **Interoperability Parameters:** The following interoperability parameters values MUST be supported:
	+ Transport: HTTP 1.1
	+ SOAP Version: 1.2
	+ Reliability Specification: none.
	+ Security Specification: WSS 1.1. 
	
### Semantics of Receipt in AS4

The notion of a Receipt alone in ebMS V3 s not associated with delivery assurance. When combined with signing it serves as both a business receipt and as a reception indicator. No particular delivery semantics can be assumed, only the following can be assumed from a message processing viewpoint:

- The related user message has been received and is well-formed
- The message has been successfully processed by the receiving message handler (none of the message handling processes over the message generated an error)

The meaning of **NOT** getting an expected Receipt is one of the following:

1. The user message was lost and never received.
2. The user message was received but the Receipt was never generated due to faulty configuration.
3. The user message was received, the Receipt was set back but lost along the way.

# ENTSOG AS4 Profile

`European network of transmission system operators for gas`

Usage profile based on OASIS ebMS 3.0 and an extended subset of the AS4 ebHandler conformance profile (for the gas community).

Defines a messaging model, message packaging, message layer security, error handling, PKI...

# PEPPOL AS4 Profile

`Pan-european public procurement online`

Facilitates becoming an access point in the PEPPOL community to enable cross-border trade. The mission statement of the OpenPEPPOL movement is to enable businesses to communicate electronically with any European government institution in the producrement process, increasing efficiency and reducing costs.