---
tags:
  - OFTP, ODETTE
---

# ODETTE File Transfer Protocol (OFTP)
> nothing like FTP

[OFTP RFC 2204](https://tools.ietf.org/html/rfc2204) -> [OFTP2 RFC 5024](https://tools.ietf.org/html/rfc5024)

Facilitates the transmission of a file between one or more locations in a way that is independent of the data communication network, system hardware and software environment. Like all the Applicability Statement protocols, it can provide digitally signed electronic delivery receipts. Current version is `OFTP2`.

It isn't built on any familiar protocol underneath.

### Where is is used

Built specifically for B2B document exchange. Though it was originall designed to work point-to-point it can be used with VANs as well.

Created in 1986, it was used for EDI between two business partners. Today it is mostly used by European car manufacturers. 

### What does it have over OFTP1

* Data compression
* Exchange of digital certificates between trading partners
* Handles massible files (>500 GB)
* Support for additional character sets, such as Chinese and Japanese

## Overview

Information is always exchanged between OFTP entities in a standard representation called a `Virtual File`. How the file is mapped from local to virtual depends will vary depending on the system. A virtual file is described by a set of attributes identifying and defining the data to be transferred. 

A single OFTP entity can make and receive calls, exchanging files in both directions. This means that OFTP2 can work in a `PULL` or `PUSH` mode, as opposed to AS2, which can only use `PUSH`

The specification is defined by going through the different "layers" or "services". Below is a short description of the layers and visualization of the structure of OFTP:

1. User Monitor - an end-user application
2. File Transfer Service - set of operations available to the user monitor
3. OFTP Entity - responsible for mapping file service operations to their respective transport service primitives. 
4. Network Service - set of operations available for communication between OFTP entities


```
          o-------------------o             o-------------------o
          |                   |             |                   |
          |   USER  MONITOR   |             |   USER  MONITOR   |
          |                   |             |                   |
          o-------------------o             o-------------------o
                  |   A                             |   A
   ...............|...|... FILE TRANSFER SERVICE ...|...|...............
                  |   |                             |   |
      F_XXX_RQ/RS |   | F_XXX_IND/CF    F_XXX_RQ/RS |   | F_XXX_IND/CF
                  V   |                             V   |
          o-------------------o             o-------------------o
          |                   |- - - - - - >|                   |
          | ODETTE-FTP Entity |   E-Buffer  | ODETTE-FTP Entity |
          |                   |< - - - - - -|                   |
          o-------------------o             o-------------------o
                  |   A                             |   A
      N_XXX_RQ/RS |   | N_XXX_IND/CF    N_XXX_RQ/RS |   | N_XXX_IND/CF
                  |   |                             |   |
   ...............|...|...... NETWORK SERVICE ......|...|...............
                  V   |                             V   |
        o---------------------------------------------------------o
        |                                                         |
        |                      N E T W O R K                      |
        |                                                         |
        o---------------------------------------------------------o

         Key:  E-Buffer - Exchange Buffer
               F_       - File Transfer Service Primitive
               N_       - Network Service Primitive		   
```

OFTP provides a file transfer `service` to a user monitor and in turn uses the transport layer to communicate between peers. These are the service classes (types of requests/responses in the entire protocol):
```
	  Request (RQ)       An entity asks the service to do some work.
      Indication (IND)   A service informs an entity of an event.
      Response (RS)      An entity responds to an event.
      Confirm (CF)       A service informs an entity of the response.
```
As you can see, the file transfer service and network service commands have a different prefix (`F_` and `N_`). The different layers are defined transparently from one another. E.g. the file transfer service commands operate on top of the network service commands.

### File Transfer Service

Describes the services offered by an OFTP entity to its User Monitor. Talking about this part right here:
```
          o-------------------o             o-------------------o
          |                   |             |                   |
          |   USER  MONITOR   |             |   USER  MONITOR   |
          |                   |             |                   |
          o-------------------o             o-------------------o
                  |   A                             |   A
   ...............|...|... FILE TRANSFER SERVICE ...|...|...............
                  |   |                             |   |
      F_XXX_RQ/RS |   | F_XXX_IND/CF    F_XXX_RQ/RS |   | F_XXX_IND/CF
                  V   |                             V   |
          o-------------------o             o-------------------o
          |                   |- - - - - - >|                   |
          | ODETTE-FTP Entity |   E-Buffer  | ODETTE-FTP Entity |
          |                   |< - - - - - -|                   |
          o-------------------o             o-------------------o
```

**In terms of an OFTP Entity operating in front of a user monitor**, these are all the commands that could come into the OFTP Entity (Input Events):
```
     F_DATA_RQ   F_CONNECT_RQ   F_START_FILE_RQ      F_CLOSE_FILE_RQ
     F_EERP_RQ   F_CONNECT_RS   F_START_FILE_RS(+)   F_CLOSE_FILE_RS(+)
     F_NERP_RQ   F_ABORT_RQ     F_START_FILE_RS(-)   F_CLOSE_FILE_RS(-)
     F_CD_RQ     F_RELEASE_RQ   F_RTR_RS
```

**These are the commands that could go out of the OFTP entity:**
```
     F_DATA_IND  F_CONNECT_IND  F_START_FILE_IND     F_CLOSE_FILE_IND
     F_EERP_IND  F_CONNECT_CF   F_START_FILE_CF(+)   F_CLOSE_FILE_CF(+)
     F_CD_IND    F_ABORT_IND    F_START_FILE_CF(-)   F_CLOSE_FILE_CF(-)
     F_NERP_IND  F_RELEASE_IND  F_DATA_CF            F_RTR_CF
```

### Network Service

OFTP peer entities communicate with each other via TCP using service primitives. The file service primitives have to be mapped to network service primitives. Now I'm talking about this part here:

```
          o-------------------o             o-------------------o
          |                   |- - - - - - >|                   |
          | ODETTE-FTP Entity |   E-Buffer  | ODETTE-FTP Entity |
          |                   |< - - - - - -|                   |
          o-------------------o             o-------------------o
                  |   A                             |   A
      N_XXX_RQ/RS |   | N_XXX_IND/CF    N_XXX_RQ/RS |   | N_XXX_IND/CF
                  |   |                             |   |
   ...............|...|...... NETWORK SERVICE ......|...|...............
                  V   |                             V   |
        o---------------------------------------------------------o
        |                                                         |
        |                      N E T W O R K                      |
        |                                                         |
        o---------------------------------------------------------o
```
**In terms of an OFTP Entity operating in front of a user monitor**, the input events are:

```
      N_CON_IND   N_CON_CF   N_DATA_IND   N_DISC_IND   N_RST_IND
```

**Likewise, the output events are:**
```
      N_CON_RQ   N_CON_RS   N_DATA_RQ   N_DISC_RQ
```

In turn, these primitives then have to be mapped to transport service primitives. Operations in the Network Service include...
- Connection Setup
- Data exchange (data exchange is an unconfirmed service.)
- Disconnect
- Network Error/TCP Reset

### Internal Commands

On top of the commands already defined, there is a third set of commands that are used strictly for communication between the OFTP Entities via exchange buffers; specifically, these commands reside in memory of each OFTP entity and operate transparently to the file transfer service, **on top of the network service**. 

The Exchange Buffer contains a single command starting at the beginning of the buffer. The buffer contains commands only, no data. 

```
      SSRM    Start Session Ready Message
      SSID    Start Session
      SECD    Security Change Direction
      AUCH    Authentication Challenge
      AURP    Authentication Response
      SFID    Start File
      SFPA    Start File Positive Answer
      SFNA    Start File Negative Answer
      DATA    Data
      CDT     Set Credit
      EFID    End File
      EFPA    End File Positive Answer
      EFNA    End File Negative Answer
      ESID    End Session
      CD      Change Direction
      EERP    End to End Response
      NERP    Negative End Response
      RTR     Ready To Receive
```

**Input events:**

```

      SSID   SFID   SFPA   SFNA   EFID   EFPA   EFNA
      DATA   ESID   EERP   RTR    CD     CDT    SSRM
      NERP   SECD   AUCH   AURP
```
**Output events:**
```
      SSID   SFID   SFPA   SFNA   EFID   EFPA   EFNA
      DATA   ESID   EERP   RTR    CD     CDT    SSRM
      NERP   SECD   AUCH   AURP
```

The input and output events are the same because at a protocol level, the speaker and listener can change.

## Protocol

OFTP has five operating phases:
1. Start Session
2. Start File
3. Data Transfer
4. End File ... Possibly more files after this
6. End Session

  
### Start Session

Entered immediately after the network connection has been established. The entity that took the initiative to establish the network connection is the "Initiator" and the peer becomes the "Responder"
  
The first message must be sent by the responder
```
1. Initiator  <-------------SSRM -- Responder   Ready Message
              - SSID ------------>              Identification
              <------------ SSID --             Identification
```
### Start File 

The initiator is designated by "Speaker" while the responder becomes the "Listener"
  
```
1. Speaker  -- SFID ------------> Listener   Start File
            <------------ SFPA --            Answer YES


2. Speaker  -- SFID ------------> Listener   Start File
            <------------ SFNA --            Answer NO
            Go To 1
```            
The Listener will send a negative answer to the Speaker when the destination is not known.

```
2. Speaker  -- CD --------------> Listener   Change Direction
   Listener <------------ EERP -- Speaker    End to End Response
            -- RTR ------------->            Ready to Receive
            <------------ SFID --            Start File			
```
The End to End Response (`EERP`) command notifies the originator of a
  `Virtual File` that it has been successfully delivered to it's final
  destination.  This allows the originator to perform house keeping
  tasks such as deleting copies of the delivered data.
After sending an `EERP`, the Speaker must wait for an `RTR` before
sending any other commands.

In order to avoid congestion between two adjacent nodes caused by a
  continuous flow of EERP's, a Ready To Receive (RTR) command is
  provided.  The RTR acts as an EERP acknowledgement for flow control
  but has no end-to-end significance.

  Speaker  -- EERP ------------> Listener   End to End Response
            <------------- RTR --            Ready to Receive
            -- EERP ------------>            End to End Response
            <------------- RTR --            Ready to Receive
            -- SFID ------------>            Start File
                      or
            -- CD -------------->            Exchange the turn   
    
### Data Transfer 
Virtual file data flows from the Speaker to the Listener during the Data Transfer phase which is entered after the Start File phase.

To avoid congestion, flow control is provided with a Credit (CDT) command. A credit limit is negotiated in the Start Session phase that represents the number of Data Exchange Buffers that the Speaker may send before it is obliged to wait for a Credit command from the Listener.
```  
    1. Speaker  -- SFID ------------> Listener   Start File
                <------------ SFPA --            Answer YES			
    2. If the Credit Value is set to 2

    Speaker  -- Data ------------> Listener   Start File
             -- Data ------------>
             <------------- CDT --            Set Credit
             -- Data ------------>
             -- EFID ------------>            End File			
```
### End File

The speaker notifies the Listener that it has finished sending a Virtual File by sending an End File (`EFID`) command. The Listener replies with a positive or negative End File command and has the option to request a Change Direction command from the speaker.

```
1. Speaker  -- EFID ------------> Listener   End File
            <------------ EFPA --            Answer YES


2. Speaker  -- EFID ------------> Listener   End File
            <------------ EFPA --            Answer YES + CD
            -- CD -------------->            Change Direction
Listener    <------------ EERP -- Speaker    End to End Response
            -------------- RTR ->            Ready to Receive
            Go to Start File Phase


3. Speaker  -- EFID ------------> Listener   End File
            <------------ EFNA --            Answer NO			
```
### End Session 

The speaker terminates the session by sending an End Session (ESID) command.
```
    1. Speaker  -- EFID ------------> Listener   End File
                <------------ EFPA --            Answer YES
                -- CD -------------->            Change Direction
    Listener    <------------ ESID -- Speaker    End Session
```
## Changes from OFTP1

Version 1.4 of ODETTE-FTP included these changes and additions to
version 1.3:

- Negative End Response (NERP)
- Extended Date and Time stamp
- New reason code 14 (File direction refused)

Version 2 added:

- Session level encryption
- File level encryption
- Secure authentication
- File compression
- Signed End to End Response (EERP)
- Signed Negative End Response (NERP)
- Maximum permitted file size increased to 9 PB (petabytes)
- Virtual file description added
- Extended error codes
