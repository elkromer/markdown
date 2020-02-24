---
tags:
	- SMS
	- Short
	- Messaging
	- Service
---

## Understanding SMS
> Bi-directional service to send text over wireless communication systems.
### Background

Originally developed as a GSM standard (Global System for Mobile Communications) by the ETSI for 2G networks but is now globally accepted. 

## Overview

When a user sends an SMS text message, the message is delivered to a Short Message Service Center (or `SMSC`). The `SMSC` will attempty to deliver the message to the inteded recipient. If the recipient is not available, it places the message in a queue for later delivery. The delivery of a message is "best effort" and not guaranteed. SMS users can request delivery reports to provide proof of delivery.

**The `SMSC` supports mobile-terminated and mobile-originated operations.** 
>The SMPP protocol, by contrast, was designed to allow the transfer of short message data between External Short Message Entities (ESME) and message centers. It basically allows applications to send and receive SMS messages.

## Technical Details

When a user sends an SMS text message, it is delivered to the `SMSC` of the user's wireless provider. The message is sent between the `SMSC` and the handset using the *Mobile application part* (`MAP`) of the *Signalling system #7* (`SS7`) protocol.
* `MAP` provides an application later to the various GSM, UMTS, GPRS nodes for intercommunication. 
* `SS7` are a set of telephony protocols used in Public Switched Telephone networks (`PTSN`)

There are a few alphabets that can be used to encode an SMS text messages. E.g. `GSM 7-Bit ASCII`, `8-Bit`, `16-bit UCS2`.

![GSM 7-Bit ASCII](/resources/sms-gsm-7.png)

An SMS text message is limited to 160 alphanumeric characters (140 octets or 1120 bits in length). Maximum individual message sizes range from 160 7-bit characters, 140 8-bit characters, or 70 16-bit characters.

## SMS Delivery

1. Message arrives at the `SMSC`
2. `SMSC` sends a request to the Home Location Register and receives routing information
3. `SMSC` sends the message to a Mobile Switching Center
4. `MSC` collects the recipients information from the Visitor Location Register (and sometimes proceeds with authentication)
5. The `MSC` forwards the message to the Mobile
6. The `MSC` reports the outcome of the Forward Short to the `SMSC`
7. The `SMSC` reports the delivery status back to the sender.

![SMS Visualization](/resources/how-sms-works.png)

## SMS and SIMs

`SIM Card`: A subscriber identity module card is an integrated circuit intended to securely store network-specific identity information for subscribers on a particular network. The SIM also stores other carrier-specific data such as the SMSC number. Most also store a number of SMS messages and phone book contacts.
> SMS messages are stored in the EF_SMS elementary file beneath the DF_TELECOM dedicated file.