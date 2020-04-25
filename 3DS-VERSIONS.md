---
tags:
	- 3DS 
	- 3D
	- Secure
	- Versions
---

# Three-Domain Secure Version Sheet

This is slightly confusing because of the differences in versioning of our libraries and 3DS libraries

Our products:

* /n software 3-D Secure (a merchant plug-in for EMV 3DS `1.0.1`)
* /n software 3-D Secure V2  

Definitions:

* **Issuer**: A Member financial institution that enters a contractual relationship with the cardholder for *issuing* them one or more payment cards. Performs enrollment of the cardholder for each payment card account via the Access Control Server.
* **Acquirer**: A member financial institution that enters into a contractual relationship with a merchant for purposes of accepting payment cards. Receives authorization requests from the merchant and forwards them to the authorization system. Provides authorization responses to the merchant.
* **Interoperability**: 


The acquirer domain and issuer domain are connected by the interoperability domain for the purpose of authenticating a cardholder duing an e-commerce transaction.

## EMVCo 3DS v1

The cardholder's browser acts as a conduit to transport messages between the Merchant Server Plug-in (in the Acquirer domain) and the Access Control Server (in the Issuer domain)

Major parts:
* Issuer
	+ Cardholder, Cardholder's browser.
  	+ Access Control Server: Two functions, **One,** to verify whether 3-D authentication is available for a particular card number and device type. **Two,** to authenticate the cardholder for a specific transaction OR to provide proof of attempted authentication when authentication is not available.
* Acquirer
	+ Merchant: Existing merchant software handles the shopping experience and obtains the card number, then invokes the Merchant Plug-in to conduct payment authentication.
	+ MPI: Creates and processes payment authentication messages, then returns control to the merchant software
* Interoperability
	+ Directory Server: receives messages from merchants querying a specific card number, determines whether the card number is participating, and directs the request for cardholder authentication to the appropriate ACS or responds directly.

![Purchase Transaction Flow](/resources/3ds-1-transactionflow.PNG)

| Step | Description |
| ----------- | ----------- |
| 1 | Shopper browses at merchant site, adds items to shopping cart, then finalizes purchase. (Note: Merchant now has all necessary data, including PAN and user device information.) |
| 2 | Merchant Server Plug-in (MPI) sends PAN (and user device information, if applicable) to the Directory Server. |
| 3 | Directory Server queries appropriate Access Control Server (ACS) to determine whether authentication (or proof of attempted authentication) is available for the PAN and device type. (If no appropriate ACS is available, the Directory Server creates a response for the MPI and processing continues with Step 5.) |
| 4 | ACS responds to Directory Server. |
| 5 | Directory Server forwards ACS response (or its own) to MPI. If neither authentication nor proof of attempted authentication is available, 3-D Secure processing ends, and the merchant, acquirer, or payment processor may submit a traditional authorization request, if appropriate. |
| 6 | MPI sends Payer Authentication Request1 to ACS via shopper’s device. |
| 7 | ACS receives Payer Authentication Request. |
| 8 | ACS authenticates shopper using processes applicable to PAN (password,chip, PIN, etc.) or creates proof of authentication. ACS then formats Payer Authentication Response2 message with appropriate values and signs it. |
| 9 | ACS returns Payer Authentication Response to MPI via shopper’s device. ACS sends selected data to Authentication History Server. |
| 10 | MPI receives Payer Authentication Response. |
| 11 | MPI validates Payer Authentication Response signature (either by performing the validation itself or by passing the message to a separate Validation Server). |
| 12 | If appropriate, merchant proceeds with authorization exchange with its acquirer. |

Following Step 12, acquirer processes authorization with issuer via an
authorization system such as VisaNet, then returns the results to
merchant.

## EMVCo 3DS v2

