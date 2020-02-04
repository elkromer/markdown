---
tags:
	- ASN
	- Abstract
	- Syntax
	- Notation
---

# Abstract Syntax Notation 1 (ASN.1)
> A notation for describing abstract types and values. 

A notation for the definition of abstract syntaxes that enable other standards to define the types of information they need to transfer. You are actually using this every day without knowing it. It works so well, it's invisible!

3G and 4G mobile standards use it, x509 certificates, voip, ldap, snmp... It has two main aspects: Associating types with information and serializing data on the wire so it can be platform-independent. 

```
ITU-T X.200 Basic Reference Model (OSI)

ITU-T X.208 Specification of ASN.1 (superceded by X.680-3S)

ITU-T X.680 series (NOTATION SYNTAX)
	- X.680 Basic notation
	- X.681 Information object specification
	- X.682 Constraint specification
	- X.683 Paramaterization

ITU X.690 series (ALL THE RULES)
	- X.690 BER, CER, DER
	- X.691 PER
	- X.692 ECN
	- X.693 XER
	- X.694 W3C XML schema definitions
	- X.695 Registration and application of PER
	- X.696 OER
	- X.697 JER
```

In ASN.1, a type is a set of values. For some types, there are a finite number of values and for other types there are an infinite number. A **value** is an element of the ASN.1 type's set. ASN.1 has four kinds of type:
- **Simple** types, "atomic" and have no components
- **Structured** types, which have components
- **Tagged** types, which are derived from other types
- **Other** types, which include `CHOICE` type and `ANY` type
## Notation Syntax

### Basic types

`typename BIT STRING ::= '0011011010100100'B`

`typename OCTET STRING ::= '36D4'H`

`string IA5String ::= "Hello world!"`

------

### Structured types

**Sequence**, An ordered list of values

```
Car ::= SEQUENCE {
	brand IA5String,
	color ENUMERATED {red, blue, grey}
	age INTEGER }
```

**Sequence of** An ordered series of elts of the same type

`myCars ::= SEQUENCE OF Car`

**Set** An unordered list of values

```
Car ::= SET {
	brand IA5String,
	color ENUMERATED {red, blue, grey}
	age INTEGER }
```

**Set of** An unordered series of elts of the same type

`myCars ::= SET OF Car` 

**Choice** the value can be one of the presented options

```
Transport ::= CHOICE {
	car carDescriptor,
	train trainTicketInfo,
	plane planeTicketInfo }
```

------

### Restricted types

**Range** The value can be anything within the range

`Age ::= INTEGER (0..50)` 

**Value set** The value can be any of the presented options

`Age ::= INTEGER {0|5|10|15|20}`

**Enumerated values** the value can be any of the presented structured options

`Color ::= ENUMERATED {red(1), blue(2), grey(3)}`

------

### Advanced types

**Object identifier** Used when a globally unique identifier is necessary. A reference to an entry in the global object identifier tree. [Global object tree...](http://www.oid-info.com/cgi-bin/display)

`oidETSI OBJECT IDENTIFIER ::= {itu-t(0) identified-organization(4) etsi(0)}`


------

## Basic Encoding Rules of ASN.1 (BER)
![Figure 1](/resources/ber-encoding-alternative.png)
The encoding of a data value shall consist of four components which shall appear in the following order:
1. Idenitifier octets
	+ Shall encode the ASN.1 tag (class and number of the type of data value)
2. Length octets
	+ The size in bytes of the encoded value.
	+ **Definite** form: One or more octets that represents the number of octets in the contents.
	+ **Indefinite** form: One octect that indicates that the contents octets are terminated by end-of-contents octets.
3. Contents octets
	+ Consists of zero or more octets that encodes the data value. The actual data of the element.
4. End-of-contents octets
	+ If the length is in indefinite form, they shall be present. Consists of two zero octets.

### Example: Simple

```
04 05 48 65 6C 6C 6F
04 05 74 68 65 72 65
```

The first byte is type `0x04` which is the universal primitive `OCTET STRING` type. The second is the length `0x05` indicating there are five bytes in the content. The remaining bytes are the encoded representations of the strings `Hello` and `there`.

### Example: LDAP Bind Request
```
BindRequest ::= [APPLICATION 0] SEQUENCE {
     version                    INTEGER (1 .. 127),
     name                       OCTET STRING,
     authentication             CHOICE {
          simple                [0] OCTET STRING,
          sasl                  [3] SEQUENCE {
               mechanism        OCTET STRING,
               credentials      OCTET STRING OPTIONAL } } }
```
```
60 16 02 01 03 04 07 63 6E 3D 74 65 73 74 80 08 70 61 73 73 77 6F 72 64
```

- The first byte is `0x60` and it is the BER type for the bind request protocol op. It comes from the [APPLICATION 0] SEQUENCE portion of the definition.
- The second byte is `0x16`, which indicates the length of the bind request sequence. `0x16` hex is 22 decimal, and the number of bytes after the `0x16` is 22.
- The next three bytes are `02 01 03`, which is a universal INTEGER value of 3. It corresponds to the version component of the bind request sequence, and it indicates that this is an LDAPv3 bind request.
- The next nine bytes are `04 07 63 6E 3D 74 65 73 74`, which is a universal OCTET STRING containing the text cn=test. It corresponds to the “name” component of the bind request sequence.
- The last component is `80 08 70 61 73 73 77 6F 72 64`, which is an element with a type of context-specific primitive 0 and a length of eight bytes. As specified in the definition of the bind request protocol op, context-specific maps to the simple authentication type and that it should be treated as an OCTET STRING, and those eight bytes in the value do represent the encoded string password.