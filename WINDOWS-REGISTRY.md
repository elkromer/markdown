---
tags:
  - Windows
  - Registry
---

# Windows Registry

A system-defined database in which applications and system components store and retrieve configuration data. Applications use the registry API to retrieve modify or delete data

To open a key, an application must supply a handle to another key in the registry that is already open. The system defines the predefined keys that are always open. 

## Specific Entries

### Standard

#### HKEY_CLASSES_ROOT

Define types of documents (file name extension associations) and the properties associated with those types as well as COM class registration information. Primarily intended for compatibility with the registry in 16-bit Windows.

#### HKEY_CURRENT_USER

Define the preferences of the current user. Preferences include environment variables, colors, printers, network connections, and application preferences.

#### HKEY_LOCAL_MACHINE

Define he physical state of the computer. Includes current configuration data, bus type, system memory, installed hardware and software, etc.

#### Wow6432Node

This is where 32-bit registry entries are stored since they must be stored separately from 64-bit. 

### COM-Related

The registry maintains information about all the COM objects installed in the system. Whenever an application creates an instance of a COM component, the registry is consulted to resolve the CLSID into the pathname of the server DLL or EXE that contains it.

#### HKEY_LOCAL_MACHINE\SOFTWARE\Classes

Contains information about an application that is needed to support COM functionality. 

#### HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Ole

Contains information that controls the default launch and access permission settings and call-level security capabilities for COM-based applications.

#### HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion

Contains information related to COM RPC debugging functionality.

## Hives

A hive is a group of keys, subkeys, and values that has a set of supporting files loaded into memory when the operating system is started or a user logs in. Most of the supporting files for hives are located in `%SystemRoot%\System32\Config`.

| Registry hive | Supporting files |
| ----------- | ----------- |
| HKEY_CURRENT_CONFIG | System, System.alt, System.log, System.sav |
| HKEY_CURRENT_USER | Ntuser.dat, Ntuser.dat.log |
| HKEY_LOCAL_MACHINE\SAM | Sam, Sam.log, Sam.sav | 
| HKEY_LOCAL_MACHINE\Security | Security, Security.log, Security.sav |
| HKEY_LOCAL_MACHINE\Software | Software, Software.log, Software.sav | 
| HKEY_LOCAL_MACHINE\System | System, System.alt, System.log, System.sav |
| HKEY_USERS\.DEFAULT | Default, Default.log, Default.sav |