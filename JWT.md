---
tags:
  - JWT
---

# JSON Web Tokens (JWTs)
>  "jots"

JWTs represent a set of claims as a JSON object that is encoded in a JSON Web Signature (JWS) and/or JSON Web Encruption (JWE) structure

### Description
A JWT is represented as a sequence of URL-safe parts separated by period ('.') characters.  Each part contains a base64url-encoded value.  The number of parts in the JWT is dependent upon the representation of the resulting JWS using the JWS Compact Serialization or JWE using the JWE Compact Serialization.

How computed {
	The following example JOSE Header declares that the encoded object is
   a JWT, and the JWT is a JWS that is MACed using the HMAC SHA-256
   algorithm:

     {"typ":"JWT",
      "alg":"HS256"}
	The octets representing the UTF-8 representation of the JOSE
   Header in this example (using JSON array notation) are:

   [123, 34, 116, 121, 112, 34, 58, 34, 74, 87, 84, 34, 44, 13, 10, 32,
   34, 97, 108, 103, 34, 58, 34, 72, 83, 50, 53, 54, 34, 125]

   Base64url encoding the octets of the UTF-8 representation of the JOSE
   Header yields this encoded JOSE Header value:

     eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9

   The following is an example of a JWT Claims Set:

     {"iss":"joe",
      "exp":1300819380,
      "http://example.com/is_root":true}

   The following octet sequence, which is the UTF-8 representation used
   in this example for the JWT Claims Set above, is the JWS Payload:

   [123, 34, 105, 115, 115, 34, 58, 34, 106, 111, 101, 34, 44, 13, 10,
   32, 34, 101, 120, 112, 34, 58, 49, 51, 48, 48, 56, 49, 57, 51, 56,
   48, 44, 13, 10, 32, 34, 104, 116, 116, 112, 58, 47, 47, 101, 120, 97,
   109, 112, 108, 101, 46, 99, 111, 109, 47, 105, 115, 95, 114, 111,
   111, 116, 34, 58, 116, 114, 117, 101, 125]

   Base64url encoding the JWS Payload yields this encoded JWS Payload
   (with line breaks for display purposes only):

     eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly
     9leGFtcGxlLmNvbS9pc19yb290Ijp0cnVlfQ

   Computing the MAC of the encoded JOSE Header and encoded JWS Payload
   with the HMAC SHA-256 algorithm and base64url encoding the HMAC value
   in the manner specified in [JWS] yields this encoded JWS Signature:

     dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk

   Concatenating these encoded parts in this order with period ('.')
   characters between the parts yields this complete JWT (with line
   breaks for display purposes only):

     eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9
     .
     eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGFt
     cGxlLmNvbS9pc19yb290Ijp0cnVlfQ
     .
     dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk
	
}

JOSE Header {
	Contents describe the cryptographic operations applied to the JWT Claims Set. If the JOSE Header is for a JWS, the JWT is represented s a JWS and the claims are digitally signed or MACed with the JWT Claims Set being the JWS Payload. If the JOSE Header is for a JWE, the JWT is represented as a JWE and the claims are encrypted, with the JWT Claims Set being the plaintext encrypted by the JWE. A JWT may be enclosed in another JWE or JWS structure to create a nested JWT.
}