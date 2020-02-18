---
tags:
  - TCL
  - Files
---

# TCL Working with Files

The commands for working with files and programs were originally designed for UNIX. In Tcl 7.5 they were implemented in the Tcl ports to Windows and Macintosh. Features were added to manipulated files in a platform-independent way.

These capabilities enable your Tcl script to be a general-purpose glue that assembles other programs into a tool that is customized for your needs.

## exec 

Runs programs from your Tcl script. Unlike UNIX `exec`, Tcl `exec` does not replace the current process with the new one. Tcl forks first and executes the program as a child process.

```tcl
set d {exec date}
```
```tcl
catch {exec program arg arg} result
```

`exec` supports a full set of I/O redirection and pipeline syntax. Each process has three I/O channels associated with it: `stdout`, `stdin`, and `stderr`. 

#### Redirection
With I/O redirection you can divert I/O channels to files or to I/O channels you opened with the Tcl `open` command.

#### Pipelines
A pipeline is a chain of processes that have the standard output of one command hooked up to the standard input of the next command in the pipeline. 

```tcl
set n [exec sort < /etc/passwd | uniq | wc -1 2> /dev/null]
```

Sort gets its input from /etc/passwd and pipes the ouput to uniq, which pipes the output to wc. The error output of the command is diverted to the null device.

#### Windows Limitations

There are problems with using process pipelines on old Windows OS's (like older than Windows 98). If a pipeline fails it is because of a fundamental limitation of Windows.

## file

Provides several ways to check the status of files in the file system. You can check if it exists, the type, and other attributes. There are also facilities for manipulating files in a platform-independent manner.

Use `file join` to construct filenames.

```tcl
set file [file join $tcl_library init.tcl]
```

`file split` divides a pathname into components.

```tcl
file split $path
=> C:/ ActiveTcl lib tcl8.6 dir1 dir2 dir3 myscript.tcl
```
Other options: `dirname`, `tail`, `extension`, `copy`, `mkdir`, `link`, `delete`, ...

Options for file attributes: `attributes`, `stat`, `lstat`, `time`, `exists`, `cutable`, `isfile`, `owned`, `readable`, `size`, `type`, ...


## open

Sets up an I/O channel to a file (or pipeline of processes). The return value.  is an identifier for the I/O channel. Store the result in a variable and use the variable as you used the `stdout`, `stdin`, and `stderr` identifiers.

```tcl
open what ?access? ?permissions?
```

The `what` argument is either a file name or a pipeline apecification similar to that used by the `exec` command.