---
tags:
  - MIME
---

# Multipurpose Internet Mail Extensions (MIME)

A set of documents based on [RFC 822 ARPA Internet Text Messages](https://tools.ietf.org/html/rfc822) that redefines the format of messages to allow for:
1. Textual message bodies in character sets other than US-ASCII.
2. An extensible set of different formats for non-textual message bodies.
3. Multi-part message bodies.
4. Textual header information in character sets other than US-ASCII.

The first document specifies the various headers used to describe the structure of MIME messages ([RFC 2045](https://tools.ietf.org/html/rfc2045))

The second document defines the general structure of the MIME media typing system and some preliminary media types ([RFC 2046](https://tools.ietf.org/html/rfc2045))

The third document describes extensions to ARPA Internet Text Messages to allow non-US-ASCII text data in mail header fields ([RFC 2047](https://tools.ietf.org/html/rfc2047))

The fourth document specifies IANA registration procedures for MIME-related facilities ([RFC 2048](https://tools.ietf.org/html/rfc2048)).

The fifth document describes MIME conformance criteria and provides illustrative examples of MIME message formats ([RFC 2049](https://tools.ietf.org/html/rfc2049)).

## RFC 822: ARPA Internet Text Messages

Messages are viewed as having an envelope and contents. The envelpe contains whatever information is needed to accomplish transmission and delivery. The contents compose the object to be delivered.

Messages consist of lines of text. It contains some information in a rigid format, followed by the main part of the message, with a format not specified in the document.  

```
     message     =  fields *( CRLF *text )       ; Everything after
                                                 ;  first null line
                                                 ;  is message body
     
     fields      =    dates                      ; Creation time,
                      source                     ;  author id & one
                    1*destination                ;  address required
                     *optional-field             ;  others optional
     
     source      = [  trace ]                    ; net traversals
                      originator                 ; original mail
                   [  resent ]                   ; forwarded
```

In general, the message consists of header fields and optionally a body. A header can be viewed as being composed of a field-name followed by a `:`, followed by a field-body, and terminated by a CRLF. The field-name must be composed of printable ASCII characters (characters that have values between 33 and 126 decimal except colon) and the field-body must be composed of any ASCII characters except CR or LF. 

The body is simply a sequence of lines containing ASCII characters separated from the headers by a null line (i.e., a line with nothing preceding the CRLF).

```
     field       =  field-name ":" [ field-body ] CRLF
     
     field-name  =  1*<any CHAR, excluding CTLs, SPACE, and ":">
     
     field-body  =  field-body-contents
                    [CRLF LWSP-char field-body]
     field-body-contents =
                   <the ASCII characters making up the field-body, as
                    defined in the following sections, and consisting
                    of combinations of atom, quoted-string, and
                    specials tokens, or else consisting of texts>

     atom        =  1*<any CHAR except specials, SPACE and CTLs>
     
     quoted-string = <"> *(qtext/quoted-pair) <">; Regular qtext or
                                                 ;   quoted chars.
     
     specials    =  "(" / ")" / "<" / ">" / "@"  ; Must be in quoted-
                 /  "," / ";" / ":" / "\" / <">  ;  string, to use
                 /  "." / "[" / "]"              ;  within a word.                                                 
```

[See the full Message spec...](https://tools.ietf.org/html/rfc822#section-4)

## RFC 2045: MIME Structure

Various headers are used to specify the structure of a MIME message. All of the header fields defined in this document are subject ot the general syntactic rules for header fields specified in ARPA Internet Text Messages.  

`Compatibility > Elegance`

Compatibility with existing standards and robustness across existing practice were the two highest concerns in the design of MIME. 

### Definitions

- `Message` = Either a complete ARPA Internet Text Message being transferred on a network, or a message encapsulated in a body of type "message/rfc822" or "message/partial"
- `Entity` = Refers to MIME-defined header fields, the contents of a message, or one of the parts in the body of a message. 
  + Any sort of field may be present in the header of an entity, but only those fields whose names begin with "content-" actually have any MIME-related meaning.
- `Body Part` = An entity inside of a multipart entity.
- `Body` = The body of an entity, the body of a message, the body of a message part. 
- `Lines` = Sequences of octets separated by a CRLF sequences. A unit of data in a message, which may or may not be displayed by a user agent.

### MIME Header Fields

`MIME-Version: 1.0` 

Required at the top level. It declares the version of the Internet message body format in use. It is not required for each body part of a multipart entity. It is required for the embedded headers of a body of type "message/rfc822" or "message/partial" if and only if the embedded message is itself claimed to be MIME-conformant.

`Content-type: text/plain; charset=us-ascii`

The Content-Type header field is to describe the data contained in the body fully enough that the receiving user agent can pick an appropriate agent or mechanism to present the data to the user. The value is called the **media type** (-> `image/xyz`). First defined in [RFC 1049](https://tools.ietf.org/html/rfc1049). After a `media type`, it can additionally be a set of unordered parameters in an `attribute=value` notation. 

The top-level media type is used to declare the general type of data (**image**/xyz), while the subtype specifies a specific format for that type of data (image/**xyz**). Parameters are modifiers of the media subtype and do not affect the nature of the content.

This default value above is assumed if no Content-Type header field is specified or a syntactically invalid Content-Type header is encountered.

`Content-ID: <part00909@roguewave.example>`

The Content-ID header associates a unique ID with a MIME part. It is designed to allow external references to a given MIME part. may be used for uniquely identifying MIME entities in several contexts. A MIME application typically creates a world-unique value to indicate the system where the MIME part originated. It has additional semantics in ([RFC 2046](https://tools.ietf.org/html/rfc2045))

`Content-Description: very simple MIME message`

The Content-Description header associates some descriptive information with a given body. Non-ASCII values must use the mechanism in ([RFC 2047](https://tools.ietf.org/html/rfc2047)).

`Content-Transfer-Encoding: 7bit`

This header specifies whether the part was processed for transfer (transfer-encoded) and how the body of the message part is currently represented. If this field value is `base64` or `quoted-printable` the body part was processed for transmission. `7bit`, `8bit`, and `binary` indicate that the body has not been processed for transmission.

### Transfer Encodings

[Quoted-Printable](https://tools.ietf.org/html/rfc2045#section-6.7) encoding is intended to represent data that largely consists of octets that correspond to printable characters in US-ASCII. It encodes the data in such a way that the resulting octets are unlikely to be modified by mail transport. If the data being encoded are largely US-ASCII text, the encoded form of the data remains largely recognizable by humans. 

It mainly provides a way to convert text into a format that is safe fore delivery through SMTP applications. It escapes characters outside of the normal ASCII character set, non-printing characters, and some whitespace characters. Some other characters like punctuation may also be escaped. Line breaks are not encoded! The encode converts any character that must be escaped into an equal sign followed by the character's ASCII value in hexadecimal. 

```
J'interdis aux marchands de vanter trop leurs marchandises. Car ils se font =
vite p=C3=A9dagogues et t'enseignent comme but ce qui n'est par essence qu'=
un moyen, et te trompant ainsi sur la route =C3=A0 suivre les voil=C3=A0 bi=
ent=C3=B4t qui te d=C3=A9gradent, car si leur musique est vulgaire ils te f=
abriquent pour te la vendre une =C3=A2me vulgaire.
```

8-bit representation: Any octet (except CR or LF) that is a part of the standard form of the data being encoded may be represented by an "=" followed by a two digit hexadecimal representation of the octet's value. This rule must be followed except when the following rules allow an alternative encoding. This is the baseline.

Literal representation: Octets with decimal values of 33-60 inclusive and 62-126 inclusive MAY be represented as the US-ASCII characters which correspond to those octets (EXCLAMATION POINT through LESS THAN, and GREATER THAN through TILDE respectively)/

White Space: Octets with values of 9 and 32 may be represented as US-ASCII TAB and SPACE characters but MUST NOT be represented at the end of an encoded line. Any TAB or SPACE character on an encoded line MUST be followed by a printable character. In particular, an "=" at the end of an encoded line, indicating a soft line break may follow one or more TAB or SPACE characters. A SPACE or TAB at the end of an encoded line MUST be represented by the 8-bit representation.

Line Breaks: CRLF in the text's standard form must be represented by Quoted-Printable. No hard line breaks that are intended to be displayed to the user can appear in Quoted-Printable.

Soft Line Breaks: QP REQUIRES that encoded lines be no more than 76 characters long. If longer lines are to be encoded, "soft" line breaks MUST be used. An equal sign as the last character on an encoded line indicates a soft line break.