---
tags:
	- Certificate
	- Certificate Chain
---

# Certificates

## X.509 Certificate
> An X.509 Certificate binds a name to a public key value. The role of the certificate is to associate a public key with the identity contained in the certificate.

Authentication of an identity in the certificate (whether it be a person, company, application, etc.) depends on the integrity of the public key value in the certificate. If an impostor replaces the public key with its own public key, it can impersonate the true identity.

To prevent this type of attack, all certificates must be signed by a certification authority, in which we confide trust that they confirm the integrity of the public key value in their certificate. 

A certificate is signed by adding its digital signature to the certificate (a message encoded with the CA's private key). Since the public key is distributed to applications, they can verify the certificates are validly signed by decoding the digital signature with the public key.

An X.509 certificate contains information about the certificate subject and certificate issuer (the CA that issued the certificate). It is encoded in [ASN.1](https://www.itu.int/ITU-T/studygroups/com17/languages/X.690-0207.pdf), a standard syntax for describing messages that can be sent or received on a network.

## Certificate Chaining
> The purpose of a certificate chain is to establish a chain of trust from a peer certificate to a trusted CA certificate. 

A certificate chain is a sequence of certs where each certificate is signed by the subsequent certificate. 

The CA vouched for the identity in the peer certificate by signing it. If the CA is one that you trust (indicated by the presence of a copy of the CA certificate your root certificate directory), this implies you trust the signed peer certificate as well.

The last certificate in a chain is normally a self-signed certificate. 

![Certificate Chain of Depth 2](https://access.redhat.com/webassets/avalon/d/Red_Hat_JBoss_Fuse-6.3-Apache_CXF_Security_Guide-en-US/images/902ef009db1036c18b70c36a19a13eb0/_manage_certs1.gif)

Certificates can be signed by multiple CAs. An application can accept a peer certificate provided it trusts at least one of the CA certificates in the signing chain.

![Certificate Chain of Depth 3](https://access.redhat.com/webassets/avalon/d/Red_Hat_JBoss_Fuse-6.3-Apache_CXF_Security_Guide-en-US/images/f31a654c9d77b7ab325b2deb2502d3f9/_manage_certs2.gif)

## Creating your own certificates

### Creating a Certificate Chain

Let's say you are the "peer" and you would like to create a certificate chain to prove your identity to something. You need to create a certificate signing request based on your private key and give it to the CA. The CA must then sign the public key contained within the CSR with their own private key and give you back a signed public key. 

Anyone looking at this certificate chain can use **your**  public key to decode the certificate and the **CA's** public key to verify the integrity of your public key.

```
├── X509CA
|   ├── CA (CA Certificate and Key)
|   ├── Int1 (Intermedaite CA Certificate and Key)
|   └── Server (Server Certificate and Key)
```

#### Generate a self-signed CA certificate and private key

```
openssl req -x509 -new -days 365 -out X509CA/CA/RootCA.pem -keyout X509CA/CA/RootCA.key
```

#### Generate an intermediate CA certificate key

```
openssl genrsa -out X509CA/Int1/IntermediateCA.key
```

#### Generate an intermediate CA certifcate signing request

```
openssl req -new -key X509CA/Int1/IntermediateCA.key -out X509CA/Int1/IntermediateCA.csr
```

A CSR is a message sent from an applicant to a CA in order to apply for a digital identity certificate. It usually contains the public key for which the certificate should be issued.

#### Sign the intermediate CA's public key using the root CA's private key

```
openssl x509 -req -days 1000 -in X509CA/Int1/IntermediateCA.csr -CA X509CA/CA/RootCA.pem -CAkey X509CA/CA/RootCA.key -CAcreateserial -out X509CA/Int1/IntermediateCA.pem
```

#### Generate the peer certificate key

```
openssl genrsa -out X509CA/Server/Server.key
```

#### Generate the peer's certificate signing request

```
openssl req -new -key X509CA/Server/Server.key -out X509CA/Server/Server.csr
```

#### Sign the server's public key using the intermediate CA's private key

```
openssl x509 -req -days 1000 -in X509CA/Server/Server.csr -CA X509CA/Int1/IntermediateCA.pem -CAkey X509CA/Int1/IntermediateCA.key -CAcreateserial -out X509CA/Server/Server.pem
```

#### Verify the certificate

```
openssl x509 -in X509CA/Server/Server.pem -noout -text
```