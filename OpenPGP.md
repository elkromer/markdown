---
tags:
  - OpenPGP
---
OpenPGP (Pretty Good Privacy)
* provides data integrity services for messages and data files through digital signatures, encryption, compression, and Radix-64 conversion. Additionally provides key management and certificate services.

The sequence of encryption for OpenPGP is as follows:
- The sender creates a message
- The sending OpenPGP generates a random number to be used as a session key for this message only
- The session key is encrypted using each recipient's public key. These "encrypted session keys" start the message.
- The sending OpenPGP symmetrically encryptes the message using the unencrypted session key.
- The receiving OpenPGP decrypts the session key using the recipient's private key
- The receiving OpenPGP decrypts the message using the session key.

Note: when the sender creates a message the sending OpenPGP also creates a hash of the message and generates a signature on the hash using the sender's private key. That structure is attached to the message. The receiving OpenPGP generates a new hash of the received message and verifies it using the attached message signature. If it verifies, then the message is accepted as authentic.

