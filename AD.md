---
tags:
	- AD
	- Active
	- Directory
---

# Active Directory (AD)
> A directory service for Windows domain networks. It is an umbrella term for a broad range of directory-based identity-related services. 

At its heart, it is a database management system. The actual database can be replicated amongst an arbiturary number of systems. The Active Directory database in an enterprise can be broken up into a units of replication called `Domains`.  

Windows uses the Active Directory as a repository for configuration information (cheifly, the storage of user logon credentials) such that computers can be configured to refer to the database to provide a centralized Single-Sign On capability for large numbers of machines. Permissions to access resources on servers that are members of an AD domain can be controlled through explicit naming of user account permissions in an Access Control List or by creating security groups for membership.

## Active Directory Domain Services (AD DS)

A server running the AD DS role is called a `domain controller`. It authenticates and authorizes all users and computers in a Windows domain-type network. AD DS provides a distributed database that stores and manages information about network resources and application-specific data from directory-enabled applications.

The heirarchical containment structure includes the AD forest, domains in the forest, and organizational units (OUs) in each domain.

```
├── Tree-Southern
|   ├── Domain-Atlanta 
|   |	├── OU-Marketing
|   |	|	├── Steve
|   |	|	├── Hewitt
|   |	├── OU-Sales
|   |	|	├── Bill
|   |	|	├── Ralph
|   ├── Domain-Dallas 
|   ├── Domain-Chapel Hill 
```

AD DS provides directory services for both the Windows server operating system and for directory-enabled applications. For the server operating system, AD DS stores critical information abou the network infrastructure, users and groups, network services, and so on. AD DS must adhere to a single schema throughout an entire forest.

## Active Directory Lightweight Directory Services (AD LDS)

AD LDS provides data storage and retrieval for directory-enabled applications without the dependencies that are required for AD DS. Provides much of the same functionality as AD DS but does not require the deployment of domains or domain controllers. You can run multiple instances of AD LDS on a single computer with an independently managed schema for each instance.

Both AD LDS and AD DS build on the same core Microsoft directory service technilogies but they address different needs in an organization.  AD LDS provides directory services specifically for directory-enabled applications

![Comparison](/resources/ad-ds-ad-lds.png)

