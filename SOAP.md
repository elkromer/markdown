---
tags:
  - SOAP, XML
---

SOAP

Primary goal: Provide a simple mechanism for exchanging structured information between peers over LAN or internet using XML. It does not provide any implementation semantics so it can be built on anything. HTTP, SMTP, POP, snail mail...

SOAP Envelope {
	Construct defines an overall framework for expressing what is in a message; who should deal with it, and whether it is optional or mandatory.
}

SOAP encoding rules {
	defines a serialization mechanism that can be used to exchange instances of application-defined datatypes.
}

SOAP RPC representation {
	defines a convention that can be used to represent remote procedure calls and responses.
}

Responsibility of a receiving SOAP application {
	MUST process the received SOAP message by performing the following actions in order:
	
	1. Identify all parts of the SOAP message intended for that application (see section 4.2.2)
	2. Verify that all mandatory parts identified in step 1 are supported by the application for this message (see section 4.2.3) and process them accordingly. If this is not the case then discard the message (see section 4.4). The processor MAY ignore optional parts identified in step 1 without affecting the outcome of the processing.
	3. If the SOAP application is not the ultimate destination of the message then remove all parts identified in step 1 before forwarding the message. 
}

Examples {
	SOAP message embedded in HTTP request {
POST /StockQuote HTTP/1.1
Host: www.stockquoteserver.com
Content-Type: text/xml; charset="utf-8"
Content-Length: nnnn
SOAPAction: "Some-URI"

<SOAP-ENV:Envelope
  xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/"
  SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/">
   <SOAP-ENV:Body>
       <m:GetLastTradePrice xmlns:m="Some-URI">
           <symbol>DIS</symbol>
       </m:GetLastTradePrice>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>	
	}
	SOAP message embedded in HTTP response {
HTTP/1.1 200 OK
Content-Type: text/xml; charset="utf-8"
Content-Length: nnnn

<SOAP-ENV:Envelope
  xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/"
  SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/"/>
   <SOAP-ENV:Body>
       <m:GetLastTradePriceResponse xmlns:m="Some-URI">
           <Price>34.5</Price>
       </m:GetLastTradePriceResponse>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>	
	}
	
}