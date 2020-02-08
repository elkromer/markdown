---
tags:
  - TCL
  - String
  - Processing
---

# String Processing in TCL

Strings are the basic data item. Both simple pattern matching and regexes are supported.

## String

The `string` command is a collection of operations you can perform on strings. The first argument to string determines the operation. These are the string operations that are used most:

- `equal`
- `match`
- `tolower`, `totitle`, `toupper`
- `trim`, `trimright`, `trimleft`

Strings can be compared in `expr`, `if`, and `while` commands using comparison operators. A safe way to compare strings is to use `string compare` and `string equal` (`==`, `!=`, `<`, and `>` are supported but not as safe). This was the way to compare strings in older versions of Tcl. 

There are a number of subtle issues that can cause problems. You must quote the string value so the parser can identify it as a string type. You must also group the expression in curly braces so the strings to prevent the double quotes from being stripped off by the interpreter.

```tcl
if {"0xa" == "10"} { puts ack! }
```
*`expr` is only reliable for string comparison when using `eq` or `ne`*

The `eq` and `ne` operators were introduced in 8.4 to allow more compact and strict string comparison. They also work faster because unnecessary conversions are eliminated. 

 ```tcl
 if {$s1 eq $s2} {
  # strings are equal
 }
 ```

## String Matching

The `string match` command implements glob-style pattern matching modeled after the file name pattern matching done by UNIX shells. 

*Globbing is the operation that expands a wildcard pattern into the list of pathnames matching the pattern*

Matching characters used with `string match`:
- `*` match any number of any characters
- `?` match exactly one character
- `[chars]` match any *single* character in chars

Any other characters in a pattern are taken as literals that must match the input exactly.

```tcl
string match {[ab]*} cello
=> 0
string match {[ab]*x} box
=> 1
```
*The set matches only a single character. To match one or more characters from a set, you will need to use regexes*

Remember that `[]` are special to the Tcl interpreter so you must wrap the pattern in curly braces if you plan to use them.

## Other Commands

### Character Classes

The `string is` command tests a string to see whether it belongs to a particular class. This is useful for input validation. To make sure something is a number:

```tcl
if {![string is integer -strict $input]} {

}
```

Classes are defined in terms of a unicode character set. Some examples are `alnum`, `ascii`, `boolean`, `digit`, `double`, `integer`, `upper`, `xdigit`, etc.

### Mapping Strings

The `string map` command translates a string based on a character map. The map is in the form of an input output list. Kind of like for those cryptography geeks that exchange certain letters for other ones.

```tcl
string map {f p d l} food
=> pool
string map {f p d ll oo u} food
=> pull
```

### Append

The `append` command takes a variable name as its first argument and concatenates its remaining arguments onto the named variable. It provides an efficient way to add items to the end of a string.

```tcl
set foo z
append foo a b c
set foo
=> zabc
```
*append is efficient with large strings.*

### Format

The `format` command is similar to the `printf` function. It formats a string according to a format specification.

```tcl
format spec value1 value2...
```

*spec* includes literals and keywords. Literals are placed into the result as-is. The keywords indicate how to format the corresponding argument.

Keywords are introduced with a `%` followed by zero or more modifiers, and terminate with a conversion specifier. Literally. 

```tcl
format "%#x" 20
=> 0x14
format "%#08x" 20
=> 0x0000000a
```

 *The `#` causes a leading 0x to be printed. The zero in `08` causes the field to be padded with zeros.*

The `%` and conversion character mark the beginning and end of the keyword. The most general keyword specification for each argument contains up to six parts.
1. position specifier
2. flags
3. field width
4. precision
5. word length
6. conversion character



```tcl
set lang 2
format "%${lang}\$s" one un uno
=> un
```
*The Tcl interpreter substitutes `${lang}` for `2`, so the spec becomes "%2\\$s". The dollar sign is escaped and used as a position specifier in the spec*

### Binary

The `binary` commands provide conversion between strings and packed binary data.

The `binary format` command takes values and packs them according to a template. The resulting binary value is returned:

```tcl
binary format template value ?value...
```

The `binary scan` command extracts values from a binary string according to a similar template. This is useful for extracting data in a binary file. It assigns values to a set of Tcl variables:

```tcl
binary scan value template var ?var...
```

*Endianness* indicates the ordering of bytes within a multi-byte number. For example, consider the unsigned hexadecimal number `0x1234`, which requires at least two bytes to represent. In a big-endian ordering they would be `12 34`, while in a little-endian ordering, the bytes would be arranged `34 12`. 