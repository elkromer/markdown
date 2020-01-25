---
tags:
  - TCL
  - Tool
  - Command
  - Language
---

# Tool Command Language (TCL)

A string-based command language. The syntax is meant to be simple. Tcl is designed to be a glue that assembles software building blocks into applications. In addition, Tcl is interpreted when the application runs. 

#### Compiled Langauges

Code written in a compiled language is converted directly into machine code during build-time. They tend to be faster to execute than interpreted languages and give the developer more control over hardward aspects like memory management and CPU usage. Every time you make a change you need to rebuild the program.

Examples: `C`, `C++`, `Erlang`, `Haskell`, `Rust`, and `Go`

#### Interpreted Languages

Interpreters will run through a program line by line and execute each command. Interpreted languages were once known to be significantly slower than compiled languages but with the development of just-in-time compilation, that gap is shrinking.

Examples: `Tcl`, `PHP`, `Ruby`, `Python`, and `JavaScript`

##  TCL Commands

Everything is cast into the mold of a command, even programming constructs like variable assignment and procedure definitions.

```tcl
command arg1 arg2 arg3 ...
```

The `command` is either the name of a built-in command or Tcl procedure. A command is terminated by either a `CRLF` or `;`. Tcl does not interpret the arguments to the commands except to perform grouping or substitution. The behavior of the Tcl command processor can be summarized in three basic steps:

#### Tcl command processor stages

1. Argument grouping
	* Words are grouped together into a single argument with `{}` or `""`
2. Value substitution of nested commands, variables, and escapes
3. Command invocation

#### Variables

The `set` command is used to assign a value to a variable. It takes two arguments, first the name of the variable and second the value. Names are case-sensitive, any length, and any character can be used.

```tcl
set var 5
set b $var
```

The second `set` command gets rewritten by subsituting the value of `var` for `$var` to obtain a new command: `set b 5`. The actual implementation of substitution is more efficient. 

#### Command Substitution

A nested command is delimited by `[]`. The Tcl interpreter takes everything between the brackets and evaluates it as a command. It rewrites the outer command by replacing the square brackets and everything between them with the result of the nested command. An artbiturary number of nested commands are supported.

```tcl
set len [string length foobar]
```

If there are several cases of command substitution within a single command, the interpreter processes them from left to right. As each right bracket is encountered, the command it delimits is evaluated. This allows nested commands to be evaluated first so that their result can be used in arguments to the outer command.

```tcl
set x 7
set len [expr [string length foobar] + 7]
=> 13
```
## Grouping 

Double quotes and curly braces are used to group words together into one argument. The difference between double quotes and curly braces is that quotes allow substitution to occur in the group and curly braces do not.

Grouping with curly braces is usually done when substitutions on the argument must be delayed until a later time or never done at all.

#### Square Brackets do not Group

A nested command is considered part of the current group. In the command below, the double quotes group the last argument, and the nested command is just a part of that group.

```tcl
puts stdout "The length of $s is [string length $s]"
```

So, if an argument is made of *only* a nested command, you do not need to group it because the Tcl parser treats the whole nested command as part of the group.

#### Grouping before Substitution

The Tcl parser makes a single pass though a command as it makes grouping decisions and performs string substitutions. The values being substituted do not affect grouping because the grouping decisions have already been made.

A nested command is treated as an unbroken sequence of characters, regardless of its internal structure. It is included with the surrounding group of characters when collecting arguments for the main command.

```tcl
set x 7; set y 9;
puts stdout $x+$y=[expr $x + $y]
=> 7+9=16
```

The white space inside the nested command is ignored for the purposes of grouping the argument. By the time Tcl encounters the left bracket, it has already done some variable substitutions to get: `7+9=`. When the left bracket is encountered, the interpreter calls itself recursively to evaluate the nested command. Again, the $x and $y are substituted before calling `expr`. Finally, the result of `expr` is substituted and gives rise to `puts` third argument: `7+9=16`.

*Grouping before substitution.*

## Notes
`expr` parses and evaluates math functions. It operates more efficiently if the arguments are grouped.
```tcl
set x 7
set len [expr {[string length foobar] + 7]}
=> 13
```