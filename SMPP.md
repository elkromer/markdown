---
tags:
	- SMPP
	- Short
	- Message
	- Peer
---

# Short Message Peer to Peer (SMPP)
>Provides an interface for transfer of short message data between a Message Center and SMS application system. 

SMPP protocol defines a set of operations for the exchange of short messages between an `ESME` and an `SMSC` *and also* the data that an `ESME` application must exchange with an `SMSC`.

Using SMPP, an SMS application system may initiate a connection with a Message Center over a TCP/IP or X.25 network connection and may then send and receive short messages from the Message Center. SMPP supports Digital Cellular Network technologies including `GCM`, `IS-95`, `ANSI-l36`, and `iDEN`.

### Terminology
Mobile entities that submit or receive message from a Message Center are known as short message entities (`SMEs`). Non-mobile entities that submit or receive message from a Message Center are known as external short message entities (`ESMEs`). Message Centers can be a Short Message Service Centre (`SMSC`), GSM Unstructured Supplementary Services Data (`GSM USSD`), or other type of Message Center.

![](/resources/smpp-defns.png)

## Overview

Every SMPP operation must consist of a request PDU and associated response PDU except for the **alert_notification** PDU for which there is no response. The exchange of message between an `ESME` and `SMSC` may be categorized under three groups of transactions:
1. Messages sent from the `ESME` transmitter to the `SMSC`
2. Messages sent from the `SMSC` to the `ESME` receiver
3. Messages sent from the `ESME` transceiver to the `SMSC` **and** message sent from the `SMSC` to the `ESME` transceiver

An SMPP session is initiated by the `ESME` first establishing a network connection with the `SMSC` and then issuing an `SMPP Bind` request to open an SMPP session. An `ESME` can bind as a transmitter, receiver, or transceiver by sending the respective bind request (e.g. a **bind_transmitter**, **bind_receiver**, or **bind_transceiver** PDU).

### Outbind

An outbind allows the `SMSC` to signal an `ESME` to start a **bind_receiver** request to the `SMSC`. This would be useful if the `SMSC` had outstanding messages for delivery to the `ESME`

![Outbind illustration](/resources/smpp-outbind.png)

### Messages sent from an ESME to SMSC

The `ESME` must be connected to the `SMSC` as an `ESME` transmitter or an `ESME` Transceiver. Examples of PDUs that can be sent to submit messages are:

- **submit_sm** 
- **data_sm**

Using a message identifier, the `ESME` can also perform the following operations:

- **query_sm**: Query the SMSC for the status of a previously submitted message
- **cancel_sm**: Cancel delivery of a message
- **replace_sm**: Replace a previously submitted message

A typical SMPP request/response sequence for an ESME Transmitter looks like this:

![SMPP Transmitter](/resources/smpp-esme-transmitter.png)

### Messages sent from an SMSC to an ESME

In order to receive messages from the `SMSC`, the `ESME` must already be bound as a receiver to the `SMSC`. Examples of messages which may be sent from an `SMSC` to an `ESME` receiver include:

- **deliver_sm**
- **data_sm**

The response from the receiver must preserve the PDU transaction sent by the `SMSC` and contain a command status indicating whether the command was accepted or invalid. Examples of responses are:

- **deliver_sm_resp**
- **data_sm_resp**

A typicall request/response sequence between an `SMSC` and `ESME` receiver looks like this:

![SMPP Receiver](/resources/smpp-esme-receiver.png)

### Duplex Message Exchange

Messages can also be exchanged in both directions if the `ESME` is bound as an `ESME` Transceiver. Ecamples of messages which may be send on an SMPP Transceiver session include:

- **data_sm**
- **submit_sm**
- **deliver_sm**

In addition, the message indentifier can be used to perform the following operations:

- **query_sm**
- **cancel_sm**
- **replace_sm**

A typical request/response sequence between an SMSC and an ESME bound as a Transceiver looks like this:

![SMPP Transceiver](/resources/smpp-esme-transceiver.png)