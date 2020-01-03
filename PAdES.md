---
tags:
	- PAdES
	- PDF
	- Advanced
	- Electronic
	- Signatures
---

# PDF Advanced Electronic Signatures (PAdES)

Portable document format provides a mechanism for electronic signatures, enabling users to exchange and view electronic documents easily and reliable, independent of the environment in which they were created or the environment in which they are viewed or printed.

These signatures are based on the Cryptographic Message Syntax technology and techniques but without the extended signature capabilities of CAdES (CMS Advanced Electronic Signatures which deals with the usage of electronic signatures in any generic file or email message). PAdES deals exclusively with PDF documents. In 2008, the International Organization for Standardization published a new standard, [ISO 32000-1](https://wwwimages2.adobe.com/content/dam/acom/en/devnet/pdf/pdfs/PDF32000_2008.pdf), identifying the ways in which an electronic signature, in the form of a digital signature, may be incorporated into a PDF document to authenticate the identity of the author and validate the integrity of the document's content.

In 2009, the ETSI (European Telecommunications Standard Institute) published a new standard, [ETSI TS 102 778-1](https://www.etsi.org/deliver/etsi_ts/102700_102799/10277801/01.01.01_60/ts_10277801v010101p.pdf), that would facilitate paperless transactions throughout Europe in conformance with European legislation.

## Digital Signatures in PDF Documents

The signature may be purely mathematical or biometric (handwritten signature, fingerprint, or retinal scan). The specific form of authentication used shall be implemented by a special software module called a **signature handler** (defined in [ISO 32000-1](https://wwwimages2.adobe.com/content/dam/acom/en/devnet/pdf/pdfs/PDF32000_2008.pdf Annex E). 

Digital signatures in ISO 32000 support two activities: adding a digital signature to a document and later checking that signature for validity. **Note**: Revocation information is a signed attribute, which means the signature handler must capture the revocation information before signing. The same thing applies to certificate chains. The signing software must capture and validate the certificate's chain before signing.

Signature information shall be contained in a signature dictionary. Signatures shall be created by computing a digest of the data and storing it in the document. To verify the signature, the digest shall be re-computer and compared with the one stored in the document. Differences in digest values indicate modifications have been made since the document was signed.