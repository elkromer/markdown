---
tags:
	- IIS
	- Internet
	- Information
	- Services
---

# Internet Information Services (IIS)

## IIS Modules

### Background

IIS Modules were introduces in IIS 7.0 and were not supported in versions < 6.0. A `module` is either a Win32 DLL (`native` module) or .NET 2.0 type contained within an assembly (`managed` module). 

## Overview

Added to a server to provide the desired functionality for your applications. Custom modules can be developed using the IIS C++ APIs or ASP.NET 2.0 APIs.

A module must be enabled before it can provide service for a particular application. 

### Native modules

In order to be added to the server, you must...
1. Install the module
2. Enable the module in an application

The first step globally registers the module with the server, making it available within each server worker process. The second step enables the module to execute within a particular application.

### Managed modules

A managed module does not require installation and can be enabled directly for each application. Any application can include their managed modules directly by registering them in their web.config file, and providing the implementation in the `/BIN`or `/App_Code` directories.

## ASP.NET Integration with IIS

### The old way of doing things

In IIS 6.0 and earlier, ASP.NET was implemented as an IIS ISAPI extension. The major limitation of this model was that services provided by ASP.NET application code were not available to non-ASP.NET requests. Also, ASP.NET modules were unable to affect certain parts of the IIS request processing that occurred before and after the ASP.NET execution path. Also called `Classic ASP.NET` integration mode in IIS. 

![ASP.NET Pipelines](/resources/iis-aspnet-pipelines.png)

### The current way of doing things

Runtime Integration. IIS and ASP.NET can use the same configuration to enable and order server modules as well as configure handler mappings. 

![Integrated Mode](/resources/iis-integrated-mode.png)

## Add TLS client authentication support

It is pretty simple to add TLS client authentication support in IIS. First, you will need to have already created a website. 

If your website has subapplications within it and a user connecting to your site should have authorization to access all the subapplications, decide to configure the website at the global level. Subapplications themselves can also contain different TLS client authentication configuration.

At the website level, make sure you have a binding for traffic on port 443. In the Manage Website dropdown on the right pane, click `Advanced Settings...` and enable `https` protocol. 

In the `SSL Settings` widget you can `require` client certificates to turn on TLS client authentication only.

You will need to set 
```
system.webServer/security/authentication/clientCertificateMappingAuthentication
``` 
to **True** for the default client certificate mapping settings. This will actually authorize users to view your web application based on the certificate they provided. The customer will need to add in their own `iisClientCertificateMappingAuthentication` tag to use their own behavior.