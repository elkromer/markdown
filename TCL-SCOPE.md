---
tags:
  - TCL
  - Procedures
  - Scope
---

# TCL Scope

By default there is a single global scope for procedure names. Variables defined outside of any procedure are global variables. Each procedure has a local scope for variables. After the procedure returns, those variables are undefined.

Variables defined outside the procedure are not visible to a procedure unless the `upvar` or `global` scope commands are used. 

### global

Global scope is the toplevel scope. A `global` command in the global scope has no effect. A variable defined at the global scope must be made available to commands inside a procedure by using the `global` command.

```tcl
global varName1 varName2 ...
```

### upvar

Use the `upvar` command when you need to pass the name of a variable (as opposed to its value) into a procedure. 

```tcl
upvar ?level? varName localVar
```

The command associates a local variable with a variable in a scope up the Tcl call stack. The level argument is optional and defaults to 1, meaning one level up the Tcl call stack. 

The following command is equivalent to `global foo`

```tcl
upvar #0 foo foo
```

upvar is useful anytime you have the name of a variable stored in another variable. For example, eliminate awkward constructs with upvar.

```tcl
puts stdout "\t$param = [set $param]"
```
```tcl
upvar 0 $param x
puts stdout "\t$param = $x"
```



