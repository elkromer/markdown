---
tags:
	- PEM
	- Privacy
	- Enhanced
	- Mail
---

# Privacy Enhancement for Internet Electronic Mail (PEM)
> Message encryption and authentication procedures for mail transfer on the Internet

[ARPA Internet Text Messages](https://tools.ietf.org/html/rfc822) `->` [PEM1 (RFC 1421)](https://tools.ietf.org/html/rfc1421) `->` [PEM2 (RFC 1422)](https://tools.ietf.org/html/rfc1422) [PEM3 (RFC 1423)](https://tools.ietf.org/html/rfc1423) [PEM4 (1424)](https://tools.ietf.org/html/rfc1424)

### History

RFC 1421 is the original document. RFC 1422 specifies supporting public-key certificates. RFC 1423 specifies algorithms, modes, and identifiers. RFC 1424 provides details of paper and electronic formats and procedures for the key management infrastructure.

Aimed at enhancing the privacy for electronic mail transferred in the Internet. The defined mechanisms were designed to be compatible with a range of mail transport agents (SMTP implementations)

## RFC 1421: Message Encryption and Authentication Procedures 

Defines encryption and authentication procedures, in order to provide privacy-enhanced mail services for electronic mail transfer in the Internet. Compatible with various key management approaches such as asymmetric and symmetric key encryption. The following facilities are provided:

1. Disclosure protection
2. Originator authenticity
3. Message integrity measures
4. Non-repudiation of origin (if using asymmetric)

Two level keying heirarchy is used to support PEM transmission:
* **Data Encrypting Keys** (DEKs)
	+ Used for encryption of message text and message integrity check (a signature). *In asymmetric environment, DEKs are also used to encrypt the signed representation of MICs in PEM messages to which confidentiality has been applied.*
* **Interchange Keys** (IKs)
	+ Use for encrypting DEKs for transmission within messages. Usually the same IK will be used for all messages sent from a given originator to a given recipient. Makes use of `Originator-ID` and `Recipient-ID` fields.
		+ When symmetric cryptography is used for DEK encryption, an IK is a single shared symmetric key. The IK is used to encrypt MICs as well as DEKs for transmission
		+ When asymmetric cryptography is used, the IK component used for DEK encryption is the public component. The IK component used for MIC encryption is the private component. Only one encrypted MIC needs to be provided in this case instead of one per recipient.

## Message Processing Procedures

When PEM processing is to be performed on an outgoing message, a DEK is generated for use in message encryption. If the chosen signature algorithm requires a key, a variant of the DEK is formed for use in the signature computation.

Four phase transformation procedure:

1. Plaintext message is accepted in a local form, using the host's native character set. 
2. Local form is converted to a canonical message text representation.
3. The canoncial form is optionally padded (depending on the algorithm) then encrypted
4. The encrypted text is encoded in a printable form

The output of the above is combined with a set of header fields carrying cryptographic control information. The resulting PEM message is passed to the electronic mail system to be included within the text portion of a transmitted message.

When a PEM message is received, the cryptographic control fields within its encapsulated header provide the information required for each authorized recipient to perfom MIC validation and decryption of the received message text. E.g. the printable encoding is converted to bitstring, encrypted portions are decrypted, MIC is validated, then the recipient PEM process converts the canonical representation to the appropriate local form.

### Encapsulated Headers in the PEM Message

One or more `Originator-ID` and/or `Originator-Certificate` fields are included in the PEM message's encapsulated header to provide recipients with an identification of the IKs used for message processing.

```
   -----BEGIN PRIVACY-ENHANCED MESSAGE-----
   Proc-Type: 4,ENCRYPTED
   Content-Domain: RFC822
   DEK-Info: DES-CBC,F8143EDE5960C597
   Originator-ID-Symmetric: linn@zendia.enet.dec.com,,
   Recipient-ID-Symmetric: linn@zendia.enet.dec.com,ptf-kmc,3
   Key-Info: DES-ECB,RSA-MD2,9FD3AAD2F2691B9A,
             B70665BB9BF7CBCDA60195DB94F727D3
   Recipient-ID-Symmetric: pem-dev@tis.com,ptf-kmc,4
   Key-Info: DES-ECB,RSA-MD2,161A3F75DC82EF26,
             E2EF532C65CBCFF79F83A2658132DB47

   LLrHB0eJzyhP+/fSStdW8okeEnv47jxe7SJ/iN72ohNcUk2jHEUSoH1nvNSIWL9M
   8tEjmF/zxB+bATMtPjCUWbz8Lr9wloXIkjHUlBLpvXR0UrUzYbkNpk0agV2IzUpk
   J6UiRRGcDSvzrsoK+oNvqu6z7Xs5Xfz5rDqUcMlK1Z6720dcBWGGsDLpTpSCnpot
   dXd/H5LMDWnonNvPCwQUHt==
   -----END PRIVACY-ENHANCED MESSAGE-----

          Example Encapsulated Message (Symmetric Case)
                            Figure 2
```

In the symmetric case, one IK component per recipient is added to the message in the `Recipient-ID-Symmetric` field when preparing ENCRYPTED, SIGNATURE-ONLY ("MIC-ONLY"), and SIGNATURE-CLEAR ("MIC-CLEAR") messages. Following the above field, a `Key-Info` field is added to the message and carries the messages's computed signature (MIC) encrypted under the recipient's IK.

```
   -----BEGIN PRIVACY-ENHANCED MESSAGE-----
   Proc-Type: 4,ENCRYPTED
   Content-Domain: RFC822
   DEK-Info: DES-CBC,BFF968AA74691AC1
   Originator-Certificate:
    MIIBlTCCAScCAWUwDQYJKoZIhvcNAQECBQAwUTELMAkGA1UEBhMCVVMxIDAeBgNV
    BAoTF1JTQSBEYXRhIFNlY3VyaXR5LCBJbmMuMQ8wDQYDVQQLEwZCZXRhIDExDzAN
    BgNVBAsTBk5PVEFSWTAeFw05MTA5MDQxODM4MTdaFw05MzA5MDMxODM4MTZaMEUx
    CzAJBgNVBAYTAlVTMSAwHgYDVQQKExdSU0EgRGF0YSBTZWN1cml0eSwgSW5jLjEU
    MBIGA1UEAxMLVGVzdCBVc2VyIDEwWTAKBgRVCAEBAgICAANLADBIAkEAwHZHl7i+
    yJcqDtjJCowzTdBJrdAiLAnSC+CnnjOJELyuQiBgkGrgIh3j8/x0fM+YrsyF1u3F
    LZPVtzlndhYFJQIDAQABMA0GCSqGSIb3DQEBAgUAA1kACKr0PqphJYw1j+YPtcIq
    iWlFPuN5jJ79Khfg7ASFxskYkEMjRNZV/HZDZQEhtVaU7Jxfzs2wfX5byMp2X3U/
    5XUXGx7qusDgHQGs7Jk9W8CW1fuSWUgN4w==
   Key-Info: RSA,
    I3rRIGXUGWAF8js5wCzRTkdhO34PTHdRZY9Tuvm03M+NM7fx6qc5udixps2Lng0+
    wGrtiUm/ovtKdinz6ZQ/aQ==
   Issuer-Certificate:
    MIIB3DCCAUgCAQowDQYJKoZIhvcNAQECBQAwTzELMAkGA1UEBhMCVVMxIDAeBgNV
    BAoTF1JTQSBEYXRhIFNlY3VyaXR5LCBJbmMuMQ8wDQYDVQQLEwZCZXRhIDExDTAL
    BgNVBAsTBFRMQ0EwHhcNOTEwOTAxMDgwMDAwWhcNOTIwOTAxMDc1OTU5WjBRMQsw
    CQYDVQQGEwJVUzEgMB4GA1UEChMXUlNBIERhdGEgU2VjdXJpdHksIEluYy4xDzAN
    BgNVBAsTBkJldGEgMTEPMA0GA1UECxMGTk9UQVJZMHAwCgYEVQgBAQICArwDYgAw
    XwJYCsnp6lQCxYykNlODwutF/jMJ3kL+3PjYyHOwk+/9rLg6X65B/LD4bJHtO5XW
    cqAz/7R7XhjYCm0PcqbdzoACZtIlETrKrcJiDYoP+DkZ8k1gCk7hQHpbIwIDAQAB
    MA0GCSqGSIb3DQEBAgUAA38AAICPv4f9Gx/tY4+p+4DB7MV+tKZnvBoy8zgoMGOx
    dD2jMZ/3HsyWKWgSF0eH/AJB3qr9zosG47pyMnTf3aSy2nBO7CMxpUWRBcXUpE+x
    EREZd9++32ofGBIXaialnOgVUn0OzSYgugiQ077nJLDUj0hQehCizEs5wUJ35a5h
   MIC-Info: RSA-MD5,RSA,
    UdFJR8u/TIGhfH65ieewe2lOW4tooa3vZCvVNGBZirf/7nrgzWDABz8w9NsXSexv
    AjRFbHoNPzBuxwmOAFeA0HJszL4yBvhG
   Recipient-ID-Asymmetric:
    MFExCzAJBgNVBAYTAlVTMSAwHgYDVQQKExdSU0EgRGF0YSBTZWN1cml0eSwgSW5j
    LjEPMA0GA1UECxMGQmV0YSAxMQ8wDQYDVQQLEwZOT1RBUlk=,
    66
   Key-Info: RSA,
    O6BS1ww9CTyHPtS3bMLD+L0hejdvX6Qv1HK2ds2sQPEaXhX8EhvVphHYTjwekdWv
    7x0Z3Jx2vTAhOYHMcqqCjA==

   qeWlj/YJ2Uf5ng9yznPbtD0mYloSwIuV9FRYx+gzY+8iXd/NQrXHfi6/MhPfPF3d
   jIqCJAxvld2xgqQimUzoS1a4r7kQQ5c/Iua4LqKeq3ciFzEv/MbZhA==
   -----END PRIVACY-ENHANCED MESSAGE-----

    Example Encapsulated ENCRYPTED Message (Asymmetric Case)
                            Figure 3
```

In the asymmetric case, one IK component per recipient is added to the message only for ENCRYPTED messages. Each `Recipient-ID` field is followed by a `Key-Info` field, containing the encrypted DEK under the IK appropriate for the specified recipient. The `Originator-Certificate` field has a `MIC-Info` field associated with it that carries the message's MIC.


