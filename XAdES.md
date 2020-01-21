---
tags:
	- XAdES
	- XML
	- Advanced
	- Electronic
	- Signatures
---

# XML-Advanced Electronic Signatures (XAdES)
> Defines additional attributes and tags, and mechanisms for incorporating them, into the existing XMLDSIG specification. 

The XML-Signature Working Group first developed a syntax for XML signatures called XML-Signature Core Syntax and Processing (also called `XMLDSIG`) that defines structure, rules, and syntax for signatures in XML documents. `XAdES` builds on `XMLDSIG` and provides definitions for new types that can be used to further qualify XMLDSIG signatures with information to be able to fulfill additional security requirements.

Basically, XML-DSig contains the core specification of signature structure in an XML document. XAdES adds more attributes and tags for additional signed properties, unsigned properties, and long term validity of the signature with timestamps.

## XML-Signature Core Syntax and Processing (XML-DSig)

Digital signature processing rules and syntax for creating and representing digital signatures. XML Signatures, like normal signatures, provide integrity, message authentication, and/or signer authentication services for data of any type, whether located within the XML that includes the signature or elsewhere.


`en·vel·op` = wrap up, cover, or surround completely.

An XML Signature may be applied to the content of one or more resources. **Enveloped** signatures (signature is a child of the data object) or **enveloping** signatures (signature is a parent of the data object) are over data within the same XML element structure as the signature; **detached** signatures are over data external to the signature element (for example, over external network resources, data objects on disk, or local data objects that reside within the same XML document as a sibiling element). 

>In the case an XML document contains a detached signature where the data object is a sibiling XML element of the signature, note that the signature is neither enveloping or enveloped.

### Signature Overview

XML Signatures are applied to arbitrary digital content via an indirection. Data objects are digested, the resulting value is placed in an element (with other information) and that element is then digested and cryptographically signed. They are represented by the `Signature` element and can be defined by the following structure (where `?` denotes zero or one occurrence; `+` denotes one or more occurrences; and `*` denotes zero or more occurrences.

```xml
<Signature ID?> 
  <SignedInfo>
    <CanonicalizationMethod />
    <SignatureMethod />
   (<Reference URI? >
     (<Transforms>)?
      <DigestMethod>
      <DigestValue>
    </Reference>)+
  </SignedInfo>
  <SignatureValue> 
 (<KeyInfo>)?
 (<Object ID?>)*
</Signature>
```
### Signature Generation Requirements
1. Create `SignedInfo` element with the specified `SignatureMethod`, `CanonicalizationMethod`, and `Reference`(s)
2. Canonicalize and then calculate the `SignatureValue` over `SignedInfo` based on algorithms specified in `SignedInfo`.
3. Construct the `Signature` element that includes `SignedInfo`, `Objects`(optional), `KeyInfo`(if required), and `SignatureValue`

### Reference Generation
1. Decide how to represent the data object in the `Transforms/dsig2:Selection` tag
2. Use `Canonicalization` to convert the data object into an octet stream (not required for binary data)
3. Calculate the digest value over the resulting data object
4. Create a `Reference` element, including the `Selection` element, `Canonicalization` element, the `DigestMethod`, and the `DigestValue`.

### Core Validation Requirements
1. Establish trust in the signing key mentioned in `KeyInfo`. Sometimes the signing key is implicitly known and `KeyInfo` is not used at all.
2. Check each `Reference` to see if the data object matches with the expected data object.
3. Cryptographic signature validation of of the signature calculated over `SignedInfo` (`SignatureValue`)
	+ The implementor processes `SignedInfo` with `CanonicalizationMethod` and `SignatureMethod` and verifies that matches `SignatureValue`
4. Reference validation of the digest contained in each `Reference` in `SignedInfo`
	+ The implementor hashes the identified and transformed content specified by `Reference` and checks that it matches its specified `DigestValue`

### XML Signature Structure
```xml
[s01] <Signature Id="MyFirstSignature" xmlns="http://www.w3.org/2000/09/xmldsig#"> 
[s02]   <SignedInfo>  
[s03]   <CanonicalizationMethod Algorithm="http://www.w3.org/2010/xml-c14n2"/> 
[s04]   <SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256"/> 
[s05]   <Reference> 
[s06]     <Transforms> 
[s07]       <Transform Algorithm="http://www.w3.org/2010/xmldsig2#transform">
[s07a]        <dsig2:Selection Algorithm="http://www.w3.org/2010/xmldsig2#xml"
 xmlns:dsig2="http://www.w3.org/2010/xmldsig2#"
 URI="http://www.w3.org/TR/2000/REC-xhtml1-20000126">
>
[s07b]        </dsig2:Selection>
[s07c]        <CanonicalizationMethod Algorithm="http://www.w3.org/2010/xml-c14n2"/>
[s07d]      </Transform> 
[s08]     </Transforms> 
[s09]     <DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256"/> 
[s10]     <DigestValue>dGhpcyBpcyBub3QgYSBzaWduYXR1cmUK...</DigestValue> 
[s11]   </Reference> 
[s12] </SignedInfo> 
[s13]   <SignatureValue>...</SignatureValue> 
[s14]   <KeyInfo> 
[s15a]    <KeyValue>
[s15b]      <DSAKeyValue> 
[s15c]        <P>...</P><Q>...</Q><G>...</G><Y>...</Y> 
[s15d]      </DSAKeyValue> 
[s15e]    </KeyValue> 
[s16]   </KeyInfo> 
[s17] </Signature>
```
`[s07a-s07b]`: The Selection element identifies the data object to be signed. The `XMLDSIG` specification identifies only two types, "xml" and "binary", but user specified types are also possible. Elements can be "selected" a variety of ways E.g.

By Id...
```xml
<dsig2:Selection Algorithm="http://www.w3.org/2010/xmldsig2#xml" URI="#MsgBody" />
<!-- ... -->
<soap:Body wsu:Id="MsgBody">
  <ex:operation xmlns:ex="http://www.example.com/">
    <ex:param1>42</ex:param1>
    <ex:param2>43</ex:param2>
  </ex:operation>
</soap:Body>
```
Or, with XPath...
```xml
<dsig2:Selection Algorithm="http://www.w3.org/2010/xmldsig2#xml" URI="">
<dsig2:IncludedXPath>/soap:Envelope/soap:Body[1]</dsig2:IncludedXPath>
<dsig2:ExcludedXPath>
/soap:Envelope/soap:Body[1]/ex:operation[1]/ex:param2[1]
</dsig2:ExcludedXPath>
</dsig2:Selection>
<!-- ... -->
<soap:Body>
  <ex:operation>
    <ex:param1>42</ex:param1>
    <ex:param2>43</ex:param2>
  </ex:operation>
</soap:Body>
```

## XAdES

Defines the qualifying information (properties) that have to be present in an electronic signature to remain valid over long periods. It also specifies definitions for new XML elements to carry these properties. Finally, it specifies ways for incorporating the qualifying information to XMLDSIG (either by direct incorporation of the qualifying information or using references to such information).

Below is a short overview of the properties:
- `SignaturePolicyIdentifier` Contains information for identifying the signature policy under which the electronic signature has been produced.
- **Validation data properties** are types able to contain validation data (certificate chains, CRLs, OCSP responses, etc.) and references to them. Jointly used with time-stamp properties to provide long term validity.
	+ `CompleteCertificateRefs` References to the CA certificates used to validate the signature.
	+ `CompleteRevocationRefs` References to the full set of revocation information used for the verification of the signature
	+ `AttributeCertificateRefs` References to the full set of Attribute Authorities certificates that have been used to validate the attribute certificate when present in the signature
	+ `AttributeRevocationRefs` The same
	+ `CertificateValues` Contains the values of certificates used to validate the signature
	+ `RevocationValues` Contains the full set of revocation information used for the verification of the signature.
	+ `AttrAuthoritiesCertValues` Contains values of the Attribute Authorities certificates that have been used to validate the attribute certificate
	+ `AttributeRevocationValues` The same
- **Time-stamp token container properties**. XAdES defines two types `GenericTimeStampType` and `XAdESTimeStampType` for allowing the inclusion of time-stamp tokens in an XMLDSIG signature. `XAdESTimeStampType` contains one or more of the following properties for further time-stamp tokens covering different parts of the signature:
	+ `SignatureTimeStamp` A time-stamp token with this property covers the digital signature value element.
	+ `AllDataObjectsTimeStamp` ... covers all the signed data objects.
	+ `IndividualDataObjectsTimeStamp` ... covers selected signed data objects.
	+ `SigAndRefsTimeStamp` ... covers the signature and references to validation data.
	+ `RefsOnlyTimeStamp` ... covers only references to validation data.
	+ `xadesv141:TimeStampValidationData` for incorporating validation data for a time-stamp token embedded in one of the XAdES time-stamp token containers.
	+ `xadesv141:ArchiveTimeStamp` ... covers signature and other properties required for providing long-term validity. 
- **Other properties** Additional properties useful in a range of environments.
	+ `SigningCertificate` Contains a reference to the signer's certificate.
	+ `SigningTime` Contains the time at which the signer claims to have performed the signing process.
	+ `DataObjectFormat` Identifies the format of a signed data object to enable the presentation to the verifier or use by the verifier in the way intended by the signer.
	+ `CommitmentTypeIndication` Indicates the commitment undertaken by the signer in signing (a) signed data object(s) in the context of the selected signature policy.
	+ `SignatureProductionPlace` Indication of the purported place where the signer claims to have produced the signature.
	+ `SignerRole` Contains the claimed or certified roles assumed by the signer in creating the signature.
	+ `CounterSignature` Contains signature(s) produced on the signature.
	