---
tags:
  - MIME
---

# Multipurpose Internet Mail Extensions (MIME)

Various headers are used to specify the structure of a MIME message. 

The "MIME-Version: X.X" header field is required at the top level. It is not required for each body part of a multipart entity. It is required for the embedded headers of a body of type "message/rfc822" or "message/partial" if and only if the embedded message is itself claimed to be MIME-conformant.

The "Content-Type" header field is to describe the data contained in the body fully enough that the receiving user agent can pick an appropriate agent or mechanism to present the data to the user. The value is called the "media type" (image/xyz). First defined in RFC 1049. After a media type, it can additionally a set of unordered parameters in an attribute=value notataion. 

  The top-level media type is used to declare the general type of data (image), while the subtype specifies a specific format for that type of data (xyz). Parameters are modifiers of the media subtype and do not affect the nature of the content.

  Content-type: text/plain; charset=us-ascii

  This default is assuimed if no Content-Type header field is specified or a syntactically invalid Content-Type header is encountered.

The "Content-ID" header may be used for uniquely identifying MIME entities in several contexts. For example, caching data referenced by the message or external-body mechanism. 

The "Content-Description" header associates some descriptive information with a given body.



Quoted-Printable encoding is intended to represent data that largely consists of octets that correspond to printable characters in US-ASCII. It encodes the data in such a way that the resulting octets are unlikely to be modified by mail transport. If the data being encoded are largely US-ASCII text, the encoded form of the data remains largely recognizable by humans. 
  
  8-bit representation: Any octet (except CR or LF) that is a part of the standard form of the data being encoded may be represented by an "=" followed by a two digit hexadecimal representation of the octet's value. This rule must be followed except when the following rules allow an alternative encoding. This is the baseline.

  Literal representation: Octets with decimal values of 33-60 inclusive and 62-126 inclusive MAY be represented as the US-ASCII characters which correspond to those octets (EXCLAMATION POINT through LESS THAN, and GREATER THAN through TILDE respectively)/
  
  White Space: Octets with values of 9 and 32 may be represented as US-ASCII TAB and SPACE characters but MUST NOT be represented at the end of an encoded line. Any TAB or SPACE character on an encoded line MUST be followed by a printable character. In particular, an "=" at the end of an encoded line, indicating a soft line break may follow one or more TAB or SPACE characters. A SPACE or TAB at the end of an encoded line MUST be represented by the 8-bit representation.

  Line Breaks: CRLF in the text's standard form must be represented by Quoted-Printable. No hard line breaks that are intended to be displayed to the user can appear in Quoted-Printable.

  Soft Line Breaks: QP REQUIRES that encoded lines be no more than 76 characters long. If longer lines are to be encoded, "soft" line breaks MUST be used. An equal sign as the last character on an encoded line indicates a soft line break.