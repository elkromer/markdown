---
tags:
  - TCL
  - Control
  - Commands
---

# TCL Control Structure Commands
> `if`, `switch`, `foreach`, `while`, `for`, `break`, `continue`, `catch`, `error`, and `return` 

Control commands have a command body that is executed later. It's important to group the command body with curly braces to avoid substitutions at the time the control structure is invoked.

Commands that involve a boolean test like `if`, `for`, and `while` use the `expr` command internally, so there is no need to invoke `expr` explicitly to evalutate their boolean test. Newlines are command terminators except when the interpreter is in the middle of a group defined by braces or double quotes

### if

Turns out you can execute the `if` statement more efficiently if you always group the expression with curly braces. 

```tcl
if {$x == 0} {
  puts stderr "Divide by zero."
} else {
  set slope [expr $y/$x]
}
```
```tcl
if {[command]} body
```

### switch 

Branch to one of many commands depending on the value of an expression.

```tcl
switch flags value { pat1 body1 pat2 body2 ... }
```
Possible flags: `-exact`, `-glob`, `-regexp`, `--` (means no flag but it is necessary when value can begin with `-`)

`default` pattern: Matches only if no other patterns match. Works only on the last pattern. 

```tcl
switch --exact -- $value {
	foo { doFoo; incr count(foo) }
	bar { doBar; incr count(bar) }
	default { incr count(other) }
}
```
```tcl
switch -regexp -- $value \
	^$key { body1 }\
	\t### { body1 }\
	{[0-9]*} { body1 }
```
```tcl
switch -glob -- $value {
	X* -
	Y* { takeXorYaction $value }
}
```

### while
```tcl
set numLines 0; set numChars 0;
while {[gets stdin line] >= 0} {
	incr numLines
	incr numChars [string length $line]
}
```
### foreach
```tcl
set i 1
foreach value {1 3 5 7 11 13 17 19 23} {
	set i [expr $i*$value]
}
set i
-> 111546435
```
```tcl
foreach x [list $a $b [foo]] {
	puts stdout "x = $x"
}
```
If you have a command that returns a short list of values then you can abuse the foreach command to assign the results of the commands to several variables all at once.
```tcl
foreach {key value} {orange 55 blue 72 red 24 green} {
	puts "$key: $value"
}
=> orange: 55
blue: 72
red: 24
green: 
```

### catch

An uncaught error aborts the execution of the script. 

More precisely, the Tcl script unwinds and the current `Tcl_Eval` procedure in the C runtime library returns `TCL_ERROR`. There are three cases:
1. interactive use: Tcl shell prints the error message
2. Tk: errors that arise during event handling trigger a call to `bgerror`, a procedure you can implement.
3. In C code, check the result of `Tcl_Eval`.

```tcl
catch command ?resultVar?
```

The first argument is a command body. The second argument is the name of a variable that will contain the result of the command or the error message if the command raises an error. `catch` returns zero (false) if there was no error caught.

```tcl
if {[catch { command arg1 arg2 ... } result]} {
	puts stderr $result
} else {
	# command was ok, result contains the return value
}
```

A longer example that is reminicent of a try/catch. The errorInfo is set by the Tcl interpreter after an error to reflect the stack trace from the point of the error

```tcl
if {[catch {
	command1
	command2
	command3
} result]} {
	global errorInfo
	puts stderr $result
	puts stderr "*** Tcl TRACE ***"
	puts stderr $errorInfo
} else {
	# command body ok
}
```
```tcl
switch [{
	command1
	command2
	...
} result] {
	0 { ;# normal completion }
	1 { ;# error case }
	2 { return $result  }
	3 { break; # break out of the loop }
	4 { continue; # continue loop }
	default { ;# user-defined error codes }
}
```

### error

Raises an error condition that terminates a script unless it is trapped with the `catch` command.

```tcl
error message ?info? ?code?
```

Tcl uses info to initialize the global errorInfo variable. code is a concise, machine-readable description of the error. 

```tcl
proc foo {} {
	error bogus
}
foo
=> bogus
```

### return

Return from a procedure. It is only really needed if return is to occur before the end of the procedure body or if a constant value needs to be returned.

