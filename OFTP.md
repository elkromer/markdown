---
tags:
  - OFTP, ODETTE
---

# ODETTE File Transfer Protocol (OFTP)
> nothing like FTP

Primary goal = {
	Facilitate the transmission of a file between one or more locations in a way that is independent of the data communication network, system hardware and software environemnt.
}

Virtual Files = {
Information is always exchanged between OFTP entities in this standard representation. It allows data transfer without regard for the nature of the communicating systems.
How the file is mapped from local to virtual depends will vary depending on the system. A virtual file is described by a set of attributes identifying and defining the data to be transferred

                              o---------o
                         Site | Local   |
                          A   | File A  |
                              o---------o
                                   |
      o----------------------- Mapping A ------------------------o
      |                            |                             |
      |                       o---------o                        |
      |                       | Virtual |                        |
      |                       |  File   |                        |
      |                       o---------o                        |
      |    o------------------------------------------------o    |
      |    |                                                |    |
      |    |                  ODETTE-FTP                    |    |
      |    |                                                |    |
      |    o------------------------------------------------o    |
      |      o---------o                        o---------o      |
      |      | Virtual |                        | Virtual |      |
      |      |  File   |                        |  File   |      |
      |      o---------o                        o----+----o      |
      |           |                                  |           |
      o------ Mapping B ------------------------ Mapping C ------o
                  |                                  |
             o---------o                        o----+----o
             | Local   | Site              Site | Local   |
             | File B  |  B                 C   | File C  |
             o---------o                        o---------o	
}

Service Description = {
	OFTP provides a file transfer service to a user monitor and in turn uses the transport layer to communicate between peers. These are the service classes:
	  Request (RQ)       An entity asks the service to do some work.
      Indication (IND)   A service informs an entity of an event.
      Response (RS)      An entity responds to an event.
      Confirm (CF)       A service informs an entity of the response.
}

Structural model = {
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
			   
}

Session take-down services (normal/abnormal release and abort) = {

3.4.4  Explanation of Session Take Down Services

            User  | OFTP |        Network       | OFTP |  User
   ---------------|------|----------------------|------|---------------
                  |      |                      |      |

   1. Normal Release

     F_RELEASE_RQ |      | ESID(R=normal)       |      | F_RELEASE_IND
   *--------------|->  ==|======================|=>  --|-------------->
     (R=normal)   |      |                      |      |


   2. User Initiated Abnormal Release

     F_RELEASE_RQ |      | ESID(R=error)        |      | F_ABORT_IND
   *--------------|->  ==|======================|=>   -|-------------->
   (R=error value)|      |                      |      | (R=error,AO=D)


            User  | OFTP |        Network       | OFTP |  User
   ---------------|------|----------------------|------|---------------
                  |      |                      |      |

   3. Provider Initiated Abnormal Release

     F_ABORT_IND  |      | ESID(R=error)        |      | F_ABORT_IND
   <--------------|-*  *=|======================|=>  --|-------------->
                  |      |                      |      |


   4. User Initiated Connection Abort

    F_ABORT_RQ    |      | N_DISC_RQ            |      | F_ABORT_IND
   *--------------|->  --|--------->..----------|->  --|-------------->
                  |      |           N_DISC_IND |      | (R=unsp.,AO=D)


   5. Provider Initiated Connection Abort

     F_ABORT_IND  |      | N_DISC_RQ            |      | F_ABORT_IND
   <--------------|-*  *-|--------->..----------|->  --|-------------->
   (R=error,AO=L) |      |           N_DISC_IND |      | (R=unsp.,AO=D)

           Key:  *        Origin of command flow
                 F_ --->  File Transfer Service primitive
                 N_ --->  Network Service primitive
                    ===>  ODETTE-FTP (OFTP) protocol message
	
}

Protocol spec = {
	Five operating phases
		-Start Session
		-Start File
		-Data Transfer
		-End File
			after this phase an OFTP entity may enter a new Start File phase or terminate the session via the End Session phase
		-End Session
	
	Commands {
      SSRM    Start Session Ready Message
      SSID    Start Session
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
      RTR     Ready To Receive
	}
	
	Flow {
		Start Session {
			Entered immediately after the network connection has been established. The entity that took the initiative to establish the network connection is the "Initiator" and the peer becomes the "Responder"
			
			FIRST MESSAGE MUST BE SENT BY THE RESPONDER
			   1. Initiator <-------------SSRM -- Responder   Ready Message
							-- SSID ------------>             Identification
							<------------ SSID --             Identification
		}
		Start File {
			The initiator is designated by "Speaker" while the responder becomes the "Listener"
			

		   1. Speaker  -- SFID ------------> Listener   Start File
					   <------------ SFPA --            Answer YES


		   2. Speaker  -- SFID ------------> Listener   Start File
					   <------------ SFNA --            Answer NO
							 Go To 1
			The Listener will send a negative answer to the Speaker when the
			   destination is not known.

			  Note: The User Monitor should take steps to prevent a loop
					situation occurring.

		   2. Speaker  -- CD --------------> Listener   Change Direction
			  Listener <------------ EERP -- Speaker    End to End Response
					   -- RTR ------------->            Ready to Receive
					   <------------ SFID --            Start File			
		
		The End to End Response (EERP) command notifies the originator of a
	   Virtual File that it has been successfully delivered to it's final
	   destination.  This allows the originator to perform house keeping
	   tasks such as deleting copies of the delivered data.
	    
		   After sending an EERP, the Speaker must wait for an RTR before
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
		}		
		Data Transfer {
			Virtual file data flows from the Speaker to the Listener during the Data Transfer phase which is entered after the Start File phase.

			To avoid congestion, flow control is provided with a Credit (CDT) command. A credit limit is negotiated in the Start Session phase that represents the number of Data Exchange Buffers that the Speaker may send before it is obliged to wait for a Credit command from the Listener.
			
		   1. Speaker  -- SFID ------------> Listener   Start File
					   <------------ SFPA --            Answer YES			
		   2. If the Credit Value is set to 2

			  Speaker  -- Data ------------> Listener   Start File
					   -- Data ------------>
					   <------------- CDT --            Set Credit
					   -- Data ------------>
					   -- EFID ------------>            End File			
		}
		End File {
			The speaker notifies the Listener that it has finished sending a Virtual File by sending an End File (EFID) command. The Listener replies with a positive or negative End File command and has the option to request a Change Direction command from the speaker.

		   1. Speaker  -- EFID ------------> Listener   End File
					   <------------ EFPA --            Answer YES


		   2. Speaker  -- EFID ------------> Listener   End File
					   <------------ EFPA --            Answer YES + CD
					   -- CD -------------->            Change Direction
			  Listener <------------ EERP -- Speaker    End to End Response
					   -------------- RTR ->            Ready to Receive
					   Go to Start File Phase


		   3. Speaker  -- EFID ------------> Listener   End File
					   <------------ EFNA --            Answer NO			
		}
		End Session {
			The speaker terminates the session by sending an End Session (ESID) command.

		   1. Speaker  -- EFID ------------> Listener   End File
					   <------------ EFPA --            Answer YES
					   -- CD -------------->            Change Direction
			  Listener <------------ ESID -- Speaker    End Session
			
		}
	}
}

Changes from v1 {
   This memo is a development of version 1.4 of ODETTE-FTP [OFTP] with
   these changes/additions:

      Session level encryption
      File level encryption
      Secure authentication
      File compression
      Signed End to End Response (EERP)
      Signed Negative End Response (NERP)
      Maximum permitted file size increased to 9 PB (petabytes)
      Virtual file description added
      Extended error codes

   Version 1.4 of ODETTE-FTP included these changes and additions to
   version 1.3:

      Negative End Response (NERP)
      Extended Date and Time stamp
      New reason code 14 (File direction refused)
}