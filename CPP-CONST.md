---
tags:
  - CPP
  - C++
  - Effective
---

# Writing Effective C++
> Based on Effective C++ 3rd Edition by Scott Meyers

## 1. Constants

### Prefer the compiler to the preprocessor

Prefer `const`, `enum`, and `inline` to `#define`. C directives may be treated as if it's not exactly part of the language. A directive's symbolic name may never be seen by compilers because it is removed by the preprocessor before compilation. 

```c++
#define ASPECT_RATIO 1.653
```

If you get an error during compilation involving the use of a constant it can be confusing since the name does not exist in the symbol table. It also can be confusing if you are using a symbolic debugger. Instead, replace the macro with a constant. 

```c++
const double AspectRatio = 1.653; 	// uppercase camelcase for macros usually
```

In addition, in the case of a floating point macro, use of a constant may yield smaller code than `#define`. The preprocessor's blind substitution could result in multiple copies of 1.653 in your object code while use of a constant should never result in more than one copy.

### Special Case: Constant Pointers

Because `const` **definitions** are typically put in header files (where many different source files will include them) it's important that the pointer be declared `const` and usually in addition to what the pointer points to. E.g. A `char*`-based string in a header file would look like:

```c++
const char * const authorName = "Scott Meyers";
```

### Special Case: Class-Specific Constants

To limit the scope of a constant to a class you must make it a member, you already know this. To ensure there is at most one copy, you must make it a static member. 

#### Syntax for newer compilers

Normally C++ requires you to **define** anything you are going to use but class-specific constants that are static and of integral type (integers, chars, bools) are an exception. 

```c++
class GamePlayer {
private:
	static const int NumTurns = 5;		// constant declaration
	int scores[NumTurns];				// use of a constant
};
```

This is called in-class initialization. *As long as you don't take their address*, you can declare them and use them without providing a definition. If you do take the address of a class-constant (or if your compiler incorrectly insists on a definition even if you don't take the address), you provide a separate definition like this:

```c++
const int GamePlayer::NumTurns;
```

#### Syntax for older compilers

It used to be illegal to provide an initial value for a static class member at its point of declaration. In cases where the above syntax cannot be used, you must put the initial value at the point of definition:

```c++
class CostEstimate {
private:
	static const double FudgeFactor;			// decl of static class constant
};
const double CostEstimate::FudgeFactor = 1.35; 	// defn of static class constant
```

But then it is not possible to get the value of a class constant during compilation of a class if the class contains a member like `GamePlayer::scores`. Compilers often insist on knowing the size of an array during compilation.

The accepted way to compensate for compilers that incorrectly forbid the in-class specification of initial values for static integral constants is known as `the enum hack`. The values of an enumerated type can be used where `int` is expected. GamePlayer could just as well be defined like this:

```c++
class GamePlayer {
private:
	enum { NumTurns = 5 };		// makes NumTurns a symbolic name for 5
	int scores[NumTurns];		// fine
};
```

Lots of code employs the enum hack so you should recognize it when you see it. In fact, it is a fundamental technique of template metaprogramming. 

### Use `const` whenever possible

The wonderful thing about `const` is it allows you to specify a semantic constraint - a particular object should not be modified - and compilers enforce that constraint. Whenever something should remain invariant you should label it constant, that way you enlist your compilers' aid in making sure the constraint isn't violated.

Outside of classes, you can use `const` for constants at the global scope, objects declared static at file, function, or block scope. Inside classes, you can use it for both static and non-static data members. For pointers you can specify whether the pointer itself is `const`, the data it points to is `const`, both, or neither.

```c++
char greeting[] = "Hello";
const char *p1 = greeting; 	//   non-const pointer, const data
char * const p2 = greeting; //   const pointer, non-const data
const char * const p3 = greeting; // const pointer, const data
```

Some of the most powerful uses of `const` stem from its application to function declarations. Within a function declaration, `const` can refer to the function's return value, to individual parameters, and for member functions, to the function as a whole. Having a function is generally inappropriate unless you are writing `operator` functions and trying to reduce the incidence of programmer error.

### Analogy with Iterators

STL Iterators are modeled on pointers, so an iterator acts much like a `T* pointer`. Declaring an iterator `const` is like declaring a pointer `const` (the third line above): The iterator isn't allowed to point to something different but the thing it points to may be modified. If you want an iterator that points to something that can't be modified (the STL analogue of a `const T* pointer`), you want a `const_iterator`. 

For simple constants, prefer `const` objects or `enums` to `#define`. Oh, and if you see any nonsense like this:

```c++
#define CALL_WITH_MAX(a,b) f((a)>(b) ? (a):(b))
```

Don't put up with it. Create a template for an inline function instead.

```c++
template <typename T>
inline void callWithMax(const T& a, const T&b)
{
	f( a>b ? a:b)
}
```

### Constant Member Functions

The purpose of `const` on member functions is to identify which member functions may be invoked on `const` objects. This makes...
1. the interface of a class easier to understand, and 
2. makes it possible to work with `const` objects.

This is crucial to writing efficient code. One of the most fundamental ways to improve a C++ program's performance is to pass objects by reference-to-const. The technique is only viable if there are `const` member functions with which to manupulate the resulting const-qualified objects.

**Member functions differing only in their constness *can* be overloaded**