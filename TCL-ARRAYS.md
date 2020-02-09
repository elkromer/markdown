---
tags:
  - TCL
  - Arrays
---

# TCL Arrays

An array is a Tcl variable with a string-valued index. Think of the index as the key and the array as a collection of related data items. In Tcl, arrays are implemented as hash tables so the cost of accessing each element is about the same.

A common use of arrays is to manage a collection of variables.

```tcl
set arr(idx) value
```

Values are accessed with $ substitution:

```tcl
set foo $arr(idx)
```

### Complex Indices

An array index can be any string (but don't use spaces). You can use complex strings to create flexible data structures.

```tcl
set {arr(I'm asking for trouble)} {I told you so.}
```
```tcl
set index {I'm asking for trouble}
set arr($index) {I told you so.}
```

*Parenthesis are not a grouping mechanism*

### Array Variables

You can use an array element as you would a simple variable. You can test for its existence with `info exists`, increment its value with incr, and append elements to it with lappend.

```tcl
if {[info exists stats($event)]} {incr stats($event)}
```

You can delete an entire array, or just a single array element with `unset`. `unset` is efficient with big data structures.

Arrays can be referenced indirectly:

```tcl
set name TheArray
set ${name}(xyz) {some value}
set x $TheArray(xyz)
=> some value
set x ${name}(xyz)
=> TheArray(xyz)
```

### array

Returns information about array variables. `names` returns the index names that are defined in the array. The order they are returned is arbitrary. 

```tcl
foreach index [array names arr pattern] {
	# use arr($index)
}
```
Other options are: `exists`, `get`, `set`, `size`, `unset`, `statistics`, ...

## Building Data Structures with Arrays

TODO