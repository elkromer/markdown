---
tags:
  - TCL
  - Lists
---

# TCL Lists

Lists in Tcl have the same structure as Tcl commands. All the grouping rules you have learned so far apply to lists. Operations include putting values into a list, getting elements from a list, counting elements, replacing elements, etc.

*Lists are often not the right way to build complicated data structures in scripts.* For that, use arrays. Lists are also not good for handling unstructured data such as user input.

A list is a sequence of values. Elements are separated by whitespace and can be grouped with curly braces or quotes.

```tcl
list arg1 arg2...
```

### Background

In early versions of Tcl, all values in a list were represented as strings. This gave performance issues with large lists. In the 8.0 runtime, the values are stored as an array of pointers to each element. Any element can be accessed with the same cost. Getting the length of the list is cheap. Appending is efficient. 

You can still run into performance issues if you use a big Tcl list like a string for output. Tcl will convert the list to a string representation if you print it to a file or manipulate it with string commands.

## Setting List Elements

### List

The `list` command consructs a list out of its arguments so that there is one list element for each argument. Any special characters in the list do not matter.

```tcl
list $x "a b" $y
```

### lappend

The lappend command is used to append elements to the end of a list. The first argument is a variable name and the rest of the arguments are added to the variable's value as new list elements.

```tcl
lappend new 1 2
```

### lset

Set an element of a list or nested list. The first argument is a list variable. The last argument is the value to set. The middle arguments, if any, specify which element to set. 

```tcl
lset new "a b c"
=> a b c
lset new 1 "d e"
=> a {d e} c
lset new 1 0 "g h"
=> a {{g h} e} c
```

Think of the middle arguments to `lset` like this: *The first middle element is the element index in the list variable which should be replaced. If a sublist is at that location, the second middle argument specifies the element in the sublist which should be replaced.*

### concat

The concat command is useful for splicing lists together. It concatenates its arguments, separating them with spaces.

```tcl
set x {4 5 6}
set y {2 3}
set z 1
concat $z $y $x
=> 1 2 3 4 5 6
```

## Getting List Elements

### llength

Returns the number of elements in the list.

```tcl
lindex {a b {c d} "e f g" h}
=> 5
```

### lindex

Returns a particular element of the list

```tcl
lindex $list end-1
```

### lrange

Returns a range of list elements.

```tcl
lrange {1 2 3 {4 5}} 2 end
=> 3 {4 5}
```

## Modifying Lists

### linsert

Inserts elements into a list value at a specified index.

```tcl
lindex {1 2} 0 new stuff
=> new stuff 1 2
```

### lreplace

Replaces a range of list elements with the new elements. If you don't specify any new elements you effectively delete elements from a list.

```tcl
set x [list a {b c} e d]
lreplace $x 1 2 B C
=> a B C d
```

## Searching Lists

### lsearch

Returns the index of a value in a list or -1 if it is not present. Glob-style and regex-style pattern matching are supported. I no type options are specified, the value converted to a string, then searched.

```tcl
set foo {the quick brown fox jumps over the lazy dog}
lsearch -inline -all $foo *o*
=> brown fox over dog
```

The `-inline` option returns the value instead of the index. `-all` returns all matching matching indices. `-integer` is a type option.

## Sorting Lists

### lsort

Sort a list in a variety of ways. A new list value is returned (not sorted in place). Basic options are , `-ascii`, `-dictionary`, `-integer`, `-real`, `-increasing`, and `-decreasing`.
```tcl
lsort -ascii {a Z n2 100}
=> Z a n100 n2
lsort -dictionary {a Z n2 100}
=> a n2 n100 Z
```

### split

Takes a string and turns it into a list by breaking it at specified characters and ensuring that the result has the proper list syntax. Robust way for turning input into a list.

```tcl
set splt {aa:bb:cc:dd:ee:ff}
=> aa bb cc dd ee ff
```

Pass an empty string as the delimiter option and split every letter into an element of the list.

### join

The inverse of split. It takes a list value and reformats it with specified characters separating the list elements. The top-level curly braces are removed. 