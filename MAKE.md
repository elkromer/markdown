---
tags:
  - MAKE
---

# GNU Make

A tool which controls the generation of executables and other files of a program. Enables the end user to build and install your package without knowing the details of how that is done.

## Rules

A rule tells Make how to execute a series of commands in order to build a `target` file from source files. It also specifies a list of `dependencies`. Make starts with the first target, called the `default goal` (Goals are the targets that make strives ultimately to update)  

```make
target:   dependencies ...
          commands
          ...
```
```
_unix_src: j2cp _sys
	cd cpp
	make /nologo unix_src_nolic
	cd ..\help
	make /nologo unix_help
	cd ..
	make /nologo _release_setup TARGET=unix_src
```

**Note:** Pattern matching may be used for the target name via the % symbol.

```

u32: IPWorks.u32 IPWorksSMIME.u32 IPWorksSSH.u32 IPWorksSSL.u32 IPWorksSSNMP.u32 IPWorksZip.u32 IBizFDMS.u32 IBizFedEx.u32 InPay.u32 InAmazon.u32 InEBank.u32 InPayPal.u32 InPtech.u32 InUPS.u32 InQB.u32 InSP.u32 InTSYS.u32 InShip.u32

u64: IPWorks.u64 IPWorksSMIME.u64 IPWorksSSH.u64 IPWorksSSL.u64 IPWorksSSNMP.u64 IPWorksZip.u64 IBizFDMS.u64 IBizFedEx.u64 InPay.u64 InAmazon.u64 InEBank.u64 InPayPal.u64 InPtech.u64 InUPS.u64 InQB.u64 InSP.u64 InTSYS.u64 InShip.u64

mac: IPWorks.mac IPWorksSMIME.mac IPWorksSSH.mac IPWorksSSL.mac IPWorksSSNMP.mac IPWorksZip.mac IBizFDMS.mac IBizFedEx.mac InPay.mac InAmazon.mac InEBank.mac InPayPal.mac InPtech.mac InUPS.mac InQB.mac InSP.mac InTSYS.mac InShip.mac


%.u64:
	gcc -c $*/cpp/debug_unix.cpp -o /tmp/x.o -m64 -DUNIX64 -Wwrite-strings -Wreturn-type -Wunused-variable
	gcc -c $*/kits/unix/src/$*.cpp -o /tmp/x.o -m64 -DUNIX64 -Wwrite-strings -Wreturn-type -Wunused-variable

%.u32:
	gcc -c $*/cpp/debug_unix.cpp -o /tmp/x.o -m32 -Wwrite-strings -Wreturn-type -Wunused-variable
	gcc -c $*/kits/unix/src/$*.cpp -o /tmp/x.o -m32 -Wwrite-strings -Wreturn-type -Wunused-variable

%.mac:
	gcc -c  variable
	gcc -c $*/kits/mac/src/$*.cpp -o /tmp/x.o  -Wwrite-strings -Wreturn-type -Wunused-variable
```