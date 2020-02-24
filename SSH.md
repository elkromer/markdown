---
tags:
  - SSH
---

# Secure Shell Transport Layer Protocol (SSH)

> [RFC 4250](https://tools.ietf.org/html/rfc4250) defines protocol assigned numbers.
> [RFC 4251](https://tools.ietf.org/html/rfc4251) defines SSH protocol architecture.

There are three major components of the SSH protocol:

- ## Transport Layer Protocol ([RFC 4253](https://tools.ietf.org/html/rfc4253))
	Typically runs on top of TCP/IP. Provides server authentication, confidentiality, and integrity protection with perfect forward secrecy. 
	
	> PFS is essentially defined as the cryptographic property of a key-establishment protocol in which the compromise of a session key or long-term private key after a given session does not cause the compromise of any earlier session
	
	Data integrity is protected by including with each packet a MAC that
	is computed from a shared secret, packet sequence number, and the
	contents of the packet.	Authentication is performed in this layer but is host-based ("am I talking to the right host?")

- ## User Authentication Protocol ([RFC 4252](https://tools.ietf.org/html/rfc4252))
	Runs on top of the transport protocol. Authenticates the client to the server. 

	* `SSH_MSG_USERAUTH_REQUEST`
	* `SSH_MSG_USERAUTH_SUCCESS`/`SSH_MSG_USERAUTH_FAILURE`

	The server keeps an record of the authentication methods that can be used at any given time. The client has the freedom to try the methods listed by the server in any order, in a `SSH_MSG_USERAUTH_REQUEST`. 

	The server can respond with either `SSH_MSG_USERAUTH_FAILURE` or `SSH_MSG_USERAUTH_SUCCESS`. The failure message can contain a "partial success" indicator used in multi-step authorization. 

- ## Connection Protocol ([RFC 4254](https://tools.ietf.org/html/rfc4254))
	 Runs on top of the user authentication protcol and transport protocol. Multiplexes the encrypted tunnel into several logical channels. Provides interactive login sessions, remote execution of commands, forwarded TCP/IP connections, and forwarded X11 connections. Consists of...
	 
	 - **Global Requests**
	 - **Channels**

	 Global Requests control the state of the remote machine. 
	 
	 Either side may open a channel and multiple channels are multiplexed into a single "connection".
	 
	 When either party wishes to open a channel, it sends a `SSH_MSG_CHANNEL_OPEN` containing the initial window size.

	 When either party wishes to terminate the channel, it sends `SSH_MSG_CHANNEL_CLOSE`. Once both sides have sent and received this message the channel is considered closed.

## Transport Layer Architecture 

- #### Protocol Version Exchange
- #### Key Exchange 
	+ Algorithm Negotiation (The first algorithm in the name-list must be the preferred. )
		+ Client sends local enabled algorithms (KEX => HOSTKEY => ENC => MAC => COMP)
		+ Server sends local enabled algorithms (KEX => HOSTKEY => ENC => MAC => COMP)
		+ Both sides make a guess at the preferred algorithm, if they make the same guess THAT algorithm must be used (This usually happens). 
			+ If not, both sides iterate over the client's kex algorithms one at a time and choose the first algorithm that satisfies the three conditions. 	
				1. Server also supports the algorithm.
				2. If the algorithm requires an encruption-capable host key then there is an encryption-capable algorithm on the server's host key algorithms list that is also supported by the client.
				3. If the algorithm requires a signature-capable host key, then there is a signature-capable algorithm on the server's host key list that is also supported by the client
		+ If no algorithm is found the connection fails.
	
- #### Diffie-Hellman Key Exchange 					
	- C generates a random number and sends mathmatically modified value (`e`) to S
	- S generates a random number and computes `f`. 
	- S receives `e` and computes `K` using its random number.
	- S generates the hash `H` over a concatenation of a bunch of different values: 
	```
		string    V_C, the client's identification string (CR and LF excluded)
		string    V_S, the server's identification string (CR and LF excluded)
		string    I_C, the payload of the client's SSH_MSG_KEXINIT
		string    I_S, the payload of the server's SSH_MSG_KEXINIT
		string    K_S, the host key
		mpint     e, exchange value sent by the client
		mpint     f, exchange value sent by the server
		mpint     K, the shared secret	
	```					
	- S generates a signature on `H` and sends both to C
	- C validates hostkey using certificates or a local database
	- C mathmatically computes `K` and verifies the signature on `H`
	
	*The key exchange produces two values: a shared secret `K`, and an exchange hash `H`. The communication is encrypted after this point.*

- #### Service Request 
	The user requests a service, usually `ssh-userauth` or `ssh-connection`.
	
	If the server supports the service and permits the client to use it, it must respond with a `SSH_MSG_SERVICE_ACCEPT`. Else it sends a `SSH_MSG_DISCONNECT` message.
	```
	byte      SSH_MSG_SERVICE_ACCEPT
	string    service name
	```	
	or...
	```	
	byte      SSH_MSG_DISCONNECT
	uint32    reason code (BELOW)
	string    description in ISO-10646 UTF-8 encoding [RFC3629]
	string    language tag [RFC3066]
	```

## Authentication Layer Architecture

- #### Authentication Request
	+ Client sends `SSH_MSG_USERAUTH_REQUEST` using the `none` method name.
	+ If no authentication is needed, the server must return `SSH_MSG_USERAUTH_SUCCESS`. Otherwise, the server must return `SSH_MSG_USERAUTH_FAILURE` and MAY return a list of acceptable methods with it. 
	+ The client can send another `SSH_MSG_USERAUTH_REQUEST` to force the server to abandon the previous authentication attempt. 
		+ Some servers have a limit on the number of failed authentication attempts. The recommended value is 20.
	+ The server sends `SSH_MSG_USERAUTH_FAILURE` if authentication was rejected. Otherwise, authentication is complete when the server has responded with `SSH_MSG_USERAUTH_SUCCESS`.

```
      byte      SSH_MSG_USERAUTH_REQUEST
      string    user name in ISO-10646 UTF-8 encoding [RFC3629]
      string    service name in US-ASCII
      string    method name in US-ASCII
      ....      method specific fields
```
```			
      byte         SSH_MSG_USERAUTH_FAILURE
      name-list    authentications that can continue
      boolean      partial success
```
```
      byte      SSH_MSG_USERAUTH_SUCCESS
```

- #### Banner Message
	+ The server can send an `SSH_MSG_USERAUTH_BANNER` message any time after authentication starts and before authentication is successful. 

## Connection Layer Architecture

The connection layer is a little strange. It consists of a channel mechanism and global requests.

- #### Global Requests
	+ Several kinds of requests affect the state of the remote end globally, independent of any channels. For example, to start TCP/IP forwarding on a specific port. 
	+ Both the client and server may send global requests at any time.

```

      byte      SSH_MSG_GLOBAL_REQUEST
      string    request name in US-ASCII only
      boolean   want reply
      ....      request-specific data follows
```
- #### Channel Mechanism
	+ When either side wishes to open a channel, it sends `SSH_MSG_CHANNEL_OPEN` with a particular [channel type](https://tools.ietf.org/html/rfc4250#section-4.9.1).
		- A "session" is a remote execution of a program. The program can be a shell, application, command, or some built-in subsystem.
	+ When either side wishes to close a channel, it sends `SSH_MSG_CHANNEL_CLOSE`. Upon receiving this message, a party must send back `SSH_MSG_CHANNEL_CLOSE` unless it already sent it. The channel is considered closed for a party once it has both sent and received `SSH_MSG_CHANNEL_CLOSE`.
	+ Many channel types have specific extensions that are specific to it. All channel-specific requests are sent in a `SSH_MSG_CHANNEL_REQUEST`
	+ Data transfer is done with `SSH_MSG_CHANNEL_DATA`
	+ Window size specifies how many bytes *the other* party can send before it must wait for the window to be adjusted. Both parties adjust the window with `SSH_MSG_CHANNEL_WINDOW_ADJUST`



## Data types
- **byte**: A byte represents an arbitrary 8-bit value (octet).  Fixed length data is sometimes represented as an array of bytes, written byte[n], where n is the number of bytes in the array.
- **boolean**: A boolean value is stored as a single byte.  The value 0 represents `FALSE`, and the value 1 represents `TRUE`.  All non-zero values MUST be interpreted as `TRUE`; however, applications MUST NOT store values other than 0 and 1.
- **uint32**: Represents a 32-bit unsigned integer.  Stored as four bytes in the order of decreasing significance (network byte order).  
For example: the value 699921578 (0x29b7f4aa) is stored as `29 b7 f4 aa`.

- **int64**: Represents a 64-bit unsigned integer.  Stored as eight bytes in the order of decreasing significance (network byte order).	  


## Messages

### Message ID Values
```
Message ID                            Value    Reference
-----------                           -----    ---------
SSH_MSG_DISCONNECT                       1     [SSH-TRANS]
SSH_MSG_IGNORE                           2     [SSH-TRANS]
SSH_MSG_UNIMPLEMENTED                    3     [SSH-TRANS]
SSH_MSG_DEBUG                            4     [SSH-TRANS]
SSH_MSG_SERVICE_REQUEST                  5     [SSH-TRANS]
SSH_MSG_SERVICE_ACCEPT                   6     [SSH-TRANS]
SSH_MSG_KEXINIT                         20     [SSH-TRANS]
SSH_MSG_NEWKEYS                         21     [SSH-TRANS]
SSH_MSG_USERAUTH_REQUEST                50     [SSH-USERAUTH]
SSH_MSG_USERAUTH_FAILURE                51     [SSH-USERAUTH]
SSH_MSG_USERAUTH_SUCCESS                52     [SSH-USERAUTH]
SSH_MSG_USERAUTH_BANNER                 53     [SSH-USERAUTH]
SSH_MSG_GLOBAL_REQUEST                  80     [SSH-CONNECT]
SSH_MSG_REQUEST_SUCCESS                 81     [SSH-CONNECT]
SSH_MSG_REQUEST_FAILURE                 82     [SSH-CONNECT]
SSH_MSG_CHANNEL_OPEN                    90     [SSH-CONNECT]
SSH_MSG_CHANNEL_OPEN_CONFIRMATION       91     [SSH-CONNECT]
SSH_MSG_CHANNEL_OPEN_FAILURE            92     [SSH-CONNECT]
SSH_MSG_CHANNEL_WINDOW_ADJUST           93     [SSH-CONNECT]
SSH_MSG_CHANNEL_DATA                    94     [SSH-CONNECT]
SSH_MSG_CHANNEL_EXTENDED_DATA           95     [SSH-CONNECT]
SSH_MSG_CHANNEL_EOF                     96     [SSH-CONNECT]
SSH_MSG_CHANNEL_CLOSE                   97     [SSH-CONNECT]
SSH_MSG_CHANNEL_REQUEST                 98     [SSH-CONNECT]
SSH_MSG_CHANNEL_SUCCESS                 99     [SSH-CONNECT]
SSH_MSG_CHANNEL_FAILURE                100     [SSH-CONNECT]	
```
### Message Disconnect Description and Reason Codes 
```
Symbolic Name                                  reason code
-------------                                  -----------
SSH_DISCONNECT_HOST_NOT_ALLOWED_TO_CONNECT          1
SSH_DISCONNECT_PROTOCOL_ERROR                       2
SSH_DISCONNECT_KEY_EXCHANGE_FAILED                  3
SSH_DISCONNECT_RESERVED                             4
SSH_DISCONNECT_MAC_ERROR                            5
SSH_DISCONNECT_COMPRESSION_ERROR                    6
SSH_DISCONNECT_SERVICE_NOT_AVAILABLE                7
SSH_DISCONNECT_PROTOCOL_VERSION_NOT_SUPPORTED       8
SSH_DISCONNECT_HOST_KEY_NOT_VERIFIABLE              9
SSH_DISCONNECT_CONNECTION_LOST                     10
SSH_DISCONNECT_BY_APPLICATION                      11
SSH_DISCONNECT_TOO_MANY_CONNECTIONS                12
SSH_DISCONNECT_AUTH_CANCELLED_BY_USER              13
SSH_DISCONNECT_NO_MORE_AUTH_METHODS_AVAILABLE      14
SSH_DISCONNECT_ILLEGAL_USER_NAME                   15		
```
### Message Channel Connection Failure Description and Reason Codes 
```
Symbolic Name                                  reason code
-------------                                  -----------
SSH_OPEN_ADMINISTRATIVELY_PROHIBITED                1
SSH_OPEN_CONNECT_FAILED                             2
SSH_OPEN_UNKNOWN_CHANNEL_TYPE                       3
SSH_OPEN_RESOURCE_SHORTAGE                          4		
```
