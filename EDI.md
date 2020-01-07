---
tags:
  - EDI
---

# Electronic Data Interchange (EDI)

The concept of businesses electronically communicating information that was traditionally communicated on paper such as purchase orders and invoices. Techincal standards exist to facilitate parties transacting such instruments without having to make special arrangements. Been around since the early 70s and there are many standards (X12, EDIFACT, ODETTE, etc.) some of which address the needs of specific industries or regions.

EDI was inspired by developments in miliary logistics. The complexity of the 1948 Berlin airlift required the development of concepts and methods to exchange vast quantities of data and information about transported goods. EDI standards were initially designed in the automotive industry.

EDI provides a technical basis for automated commercial conversations between two internal or external entities. The term EDI encompasses the entire EDI process including the transmission, message flow, document format, and software used to interpret the documents; however, EDI standards describe the rigorous format of electronic documents to be independent of communication and software technologies.

### Some major sets of EDI standards

- The UN-recommended UN/EDIFACT is the only international standard and is predominant outside of North America.
- The US standard ANSI ASC X12 (X12) is predominant in North America.
- GS1 EDI set of standards developed the GS1 predominant in global supply chain
- The TRADACOMS standard developed by the ANA (Article Number Association now known as GS1 UK) is predominant in the UK retail industry.
- The ODETTE standard used within the European automotive industry
- The VDA standard used within the European automotive industry mainly in Germany
- HL7, a semantic interoperability standard used for healthcare data.
- HIPAA, The Health Insurance Portability and Accountability ACT (HIPAA), requires millions of healthcare entities who electronically transmit data to use EDI in a standard HIPAA format.
- IATA Cargo-IMP, IATA Cargo-IMP stands for International Air Transport Association Cargo Interchange Message Procedures. - It's an EDI standard based on EDIFACT created to automate and standardize data exchange between airlines and other parties.
- NCPDP Script, SCRIPT is a standard developed and maintained by the National Council for Prescription Drug Programs (NCPDP). The standard defines documents for electronic transmission of medical prescriptions in the United States.
- Edig@s (EDIGAS) is a standard dealing with commerce, transport (via pipeline or container) and storage of gas.

### Transmission protocols

EDI can be transmitted using any methodology agreed to by the sender and recipient. As more trading partners began using the interent for transmission, standardized protocols have emerged.

- mModem (asynchronous and synchronous)
- FTP, SFTP and FTPS
- Email
- HTTP
- AS1
- AS2
- AS4
- OFTP (and OFTP2)
- Mobile EDI
- And more technologies

### Interpreting data

EDI translation software provides the interface between internal system and the EDI format sent/received. For an "inbound" document, the EDI solution will:

1. Receive the file 
2. Take the received EDI file (commonly called "envelope"), and validate that the trading partner who is sending the file is a valid trading partner
3. Validate that the structure of the file meets the EDI standards and the individual fields of information conform to the agreed upon standards
4. Optionally convert/transform the file into a format that can be imported into a company's back-end business systems, applications or ERP
5. Import the transformed file into the back-end system

For an "outbound" document the process for integrated EDI is to:

1. export a file from a company's information systems and transform the file to the appropriate format
2. the translation software will then validate the EDI file to ensure it meets the agreed upon standard
3. convert the file into "EDI" format by adding appropriate identifiers and control structures
4. send the file to the trading partner