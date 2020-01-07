---
tags:
  - LDAP
---

# Lightweight Directory Access Protocol (LDAP)

Provides access to the X.500 Directory (a personal or company Directory Information Tree) primarily for simple management applications and browser applications that just need to be able to do simple read/writes. Should minimize the complexity of clients so as to facilitate widespread deployment of applications capable of utilizing the Directory.

## Types of Responses

- **Unsolicited Notification**: LDAP Message sent from the server to the client that is not in response to any LDAPMessage.
- **Notice of Disconnection**: A notification used by the server to advise the client that the server is about to terminate the LDAP session on its own initiative.
- **Intermediate Response Message:** The search operation provides a mechanism for returning multiple responses already. IRM provies a general mechanism for multiple responses to be used in conjunction with the Extended operation

## Operations

- **Bind:** Allows authenticaiton information to be exchanged between the client and server. The "authenticate" operation
- **Unbind**: Terminate an LDAP session. The "quit" operation
- **Modify**: Allows a client to request that a modification of an entry be performed.
- **Add**: Allows a client to request the addition of an entry into the Directory.
- **Delete**: Allows a client to request the removal of an entry from the Directory.
- **ModifyDN**: Allows a client to change the Relative Distinguished Name of entry or move a subtree of entriess to a new location in the Directory.
- **Compare**: Allows a client to compare an assertion value with the values of a particular attribute in a particular entry in the Directory.
- **Abandon**: Allows a client to request that the server abandon an uncompleted operation
- **Extended**: Allows additional operations to be defined for services not already available in the protocol.
- **Search**: Used to request a server to return a set of entries matching a complex search criterion.

## Search
  baseObject: the name of the base object entry relative to which the search should be performed
  scope: the scope of the search 
        baseObject: scope is constrained to the entry named by baseObject
        singleLevel: scope is constrained to the immediate children of baseObject.
        wholeSubtree: scope is constrained to all the subordinates of the baseObject.
  derefAliases: indicator whether or not to derefence aliases during stages of the search operation.
  sizeLimit: Maximum number of entries to be returned
  timeLimit: Maximim time limit in seconds allowed for a search.
  typesOnly: An indicator as to whether search operations are to contain just descriptions (TRUE) or both attribute descriptions and values(FALSE).
  filter: the conditions that must be fulfilled in order for the search to match a given entry
  attributes: A selection list of the attributes to be returned from each entry that matches the search filter.
        either an attributedescription or selector special ("1.1" - noattrs, "*" - alluserattrs)

