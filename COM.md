---
tags:
  - COM
  - Component
  - Object 
  - Model
---

# Microsoft Component Object Model (COM)

In the headspace of wanting to create binary software components which can interact, Microsoft COM is a standard (just a model). 

Think of it this way: The COM is a set of programming requirements that enable `COM Objects` to interact with other `COM Objects` (a.k.a `COM components` or sometimes simply `objects`). It's about the communication between COM objects.

## Background

COM is independent of implementation language, which means that you can create COM libraries by using C++ or .NET. The essence of COM is a language-neutral way of implementing objects that can be used in environments different from the one in which they were created, even across machine boundaries. COM allows reuse of objects with no knowledge of their internal implementation.

Looking broadly across the implementations of COM components, they may be structurally very dissimilar. Like **wildly** different y'all. COM is a referred to as a `binary standard` "a standard that applies after a program has been translated to binary machine code" [[MSFT, The COM](https://docs.microsoft.com/en-us/windows/win32/com/the-component-object-model)]

In network computing, COM defines a standard wire format and protocol for interaction among objects that run on different hardware platforms. 

There is a COM library for Tcl called `tcom`, COM libraries for VBScript, JavaScript, Rust, Java... etc. To get started with COM, you need to launch a COM component and call a function on it. 

## Eclipse of .NET

COM has been replaces to some extent by .NET Framework and now .NET Core.  COM Objects can be used with all .NET languages through [.NET COM Interop](https://en.wikipedia.org/wiki/COM_Interop) mainly `Tlbimp.exe`. It enables COM objects to interact with .NET objects and vice versa. 

## Windows COM library

Provided as a set of DLLs and EXEs: `Ole32.dll` and `Rpcss.exe`. Applications can instantiate a COM control from the library or register their own COM controls on the system. 

Windows comes shipped with a bunch of applications which already use COM controls for interop. If you were to filter and display all the registry operations happening in the HKEY_LOCAL_MACHINE\Software\

## COM IUnknown

All COM interfaces inherit from the `IUnknown` interface. The IUnkown interface has 3 member functions: `QueryInterface`, `Release`, `AddRef`.

QueryInterface provides polymorphism for COM (Classes which inherit a COM interface will implement their own QueryInterface).

`Release` and `AddRef` are there for instance lifetime management. Lifetime is controlled by its reference count. AddRef increases the count and Release decrements it. 

