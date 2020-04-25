---
tags:
	- SAM
	- Security
	- Account
	- Manager
---

# Security Account Manager (SAM)

A database file on Windows XP, Vista, 7, 8.1, and 10 that stores users' passwords (a.k.a the `domain directory database`). The passwords are hashed and stored in a registry hive either as an LM hash or NTLM hash.

`C:\Windows\System32\config\SAM`

The SAM file cannot be moved or copied while Windows is running since the Windows kernel obtains and keeps an exclusive filesystem lock on the SAM file, and won't remove it unless the operating system has shut down or a blue screen of death exception has been thrown. The in-memory copy of the SAM file can be dumped.

### Background

Windows computers can be configured to be in a workgroup or joined to a domain. In a workgroup, each computer holds its own `SAM` containing information about the local users and group accounts. The local security authority (`LSA`) validates a user's logon by verifying their credentials against the data stored in the `SAM`.

When a Windows server is "promoted" to a domain controller (`DC`), it will use the `AD` database instead of the `SAM` to store data. On a domain-joined computer there can be two types of logons: a local logon handled by the `SAM` (above), or a domain user logon using the active directory database with the WinLogon service. **When a user on a DC logs in as a local user, they won't have access to network resources.** 