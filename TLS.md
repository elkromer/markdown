---
tags:
  - TLS
---

# Transport Layer Security (TLS)

Provides privacy and data integrity between two communicating applications.

## Layers 

- ### TLS Handshake Protocol 
  Allows the client and server to authenticate each other and to negotiate an encryption algorithm and cryptography keys before the application transmits any data. Three basic properties
    + A peers identity can be authenticated using asymmetric cryptography
    + Negotiation of shared secret is secure (negotiated secret is unavailable to eavesdroppers)
    + Negotiation of shared secret is reliable (no attacker can modify the negotiation communication without being detected).
			
- ### TLS Record Protocol 
  Provides connection security. Mainly used for encapsulation of various higher-level protocols. Two basic properties
    + Connection is private. Symmetric cryptography used for data encryption. Keys generated uniquely for each connection and based on a secret negotiated by TLS Handshake protocol.
    + Connection is reliable. A "secure" hash function that uses a secret (keyed MAC) is used as a checksum 

----------------------------------------

TLS Handshake Protocol
  Use to negotiate the security parameters. 
    -ClientHello: Required as the client's first TLS message when it wants to connect to a server. 
      struct {
          ProtocolVersion legacy_version = 0x0303;    /* TLS v1.2 */
          Random random;
          opaque legacy_session_id<0..32>;
          CipherSuite cipher_suites<2..2^16-2>;
          opaque legacy_compression_methods<1..2^8-1>;
          Extension extensions<8..2^16-1>;
      } ClientHello;
    -ServerHello: Sent in response to a ClientHello to proceed with the handshake
      struct {
          ProtocolVersion legacy_version = 0x0303;    /* TLS v1.2 */
          Random random;
          opaque legacy_session_id_echo<0..32>;
          CipherSuite cipher_suite; // The selected cipher suite
          uint8 legacy_compression_method = 0;
          Extension extensions<6..2^16-1>;
      } ServerHello;    
    -After these messages both sides have all the information they need to calculate the keys that are used to encrypt the rest of the handshake.
    -The connection including the handshake is encrypted from this point onwards.
    -Certificate: The server sends a message whenever the agreed upon key-exchange algorithm uses certificates for authentication
      struct {
          select (certificate_type) {
              case RawPublicKey:
                /* From RFC 7250 ASN.1_subjectPublicKeyInfo */
                opaque ASN1_subjectPublicKeyInfo<1..2^24-1>;

              case X509:
                opaque cert_data<1..2^24-1>;
          };
          Extension extensions<0..2^16-1>;
      } CertificateEntry;    
    -CertificateVerify: The server sends explicit proof that it possesses the private key corresponding to the certificate. This also provides integrity for the handshake up to this point.
      struct {
          SignatureScheme algorithm;
          opaque signature<0..2^16-1>;
      } CertificateVerify; 
    -Finished: The server sends a finished message that contains verification data. It is up to the client to verify the contents are correct and if incorrect terminate the connection. Once the server has received the finished message from its peer it may begin to send and receive application data.
      struct {
          opaque verify_data[Hash.length];
      } Finished;
    -Finished: The client sends a finished message to verify the handshake was successful. 

TLS Record Protocol 
	Takes messages to be transmitted, fragments the data into blocks, (optionally compresses the data), applies a keyed MAC, encrypts, and transmits the result. Received data is decrypted, verified, decompressed, reassembled, and then delivered to higher layers.
	
	Connection States contiain 
		A TLS connection state is the operating environment of the TLS record protocol. Basically the state of the encryption. It specifies a compression algorithm, encryption algorithm, and MAC algorithm. There will be a state for BOTH directions, called read and write. 
		
		Logically, there are always four outstanding states: the CURRENT read and write states and the PENDING read and write states. 
		
		The security parameters for the pending state are set by the TLS handshake protocol. The Change Cipher Spec protocol can selectively make either of the pending states current.
		
		The SecurityParameters contain 
			connection end (whether it is a client or server)
			bulk encryption algorithm
			MAC algorithm
			compression algorithm
			master secret (48-byte secret shared between the two peers)
			client random (32-byte)
			server random (32-byte)
		
		
		each connection state contains 
			compression state
			cipher state 
			MAC secret
			sequence number
		
	
	and must be updated for each record processed. 
	
	SecurityParameters are used by the record layer to generate 
		client write MAC secret
		server write MAC secret
		client write key
		server write key
		
		The client write parameters are used by the server when receiving and processing records.
		The server write parameters are used by the client when receiving and processing records.
	
	Once the security parameters have been set and the keys have been generated, the connection states can be instantiated by making them the current states.


TLS Handshaking Protocols 
	https://tools.ietf.org/html/rfc4346#section-7
