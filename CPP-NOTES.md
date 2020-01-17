---
tags:
  - CPP
  - C++
  - Features
---

# General C++ Notes

## Welcome back to C++
C++ is one of the most widely used programming languages in the world. Well-written programs are fast and efficient, the language is more flexible than other languages because it enables you to access low-level hardware features to maximize speed and minimize memory requirements.

Regardless of your programming background, C++ is likely to take a little getting used to. It's a powerful language with an enormous range of features. 

Backwards compatibility with C was one of the original requirements for C++. The evolution of C++ features greatly reduce the need to use C-style idioms. 

### C++ Heritage

In the beginning, C++ was just C with some object-oriented features tacked on. It's original name was "C with Classes". As it got older, it grew more bold and adventurous adopting ideas, features, and programming strategies different from those of C with Classes. Exceptions required different approaches to structuring functions. Templates gave rise to new ways of thinking about design, and the STL defined an approach to extensibility unlike any most people had ever seen.

Now C++ is a *multiparadigm programming language*, one supporting a combination of procedural, object-oriented, functional, generic, and metaprogramming features. How do you make sense of a language where all the "proper usage" rules have exceptions? The easiest way to make sense of it all is to view C++ as a federation of related languages. Within a particular sublanguage the rules tend to be simple, straightforward, and easy to remember. 

### The C++ sub-languages

- **C**: Blocks, statements, the preprocessor, built-in data types, arrays, pointers, etc. all come from C. The rules for effective programming reflect C's more limited scope: no templates, no exceptions, no overloading, etc.
- **Object-Oriented C++**: This part of C++ is what "C with Classes" was all about. Classes, constructors, destructors, encapsulation, inheritance, polymorphism, virtual functions (dynamic binding), etc. The classic rules of OO design most directly apply here.
- **Template C++**: The generic programming part of C++ (the part most have the least experience with). Templates are so powerful they give way to their own programming paradigm, *template metaprogramming* (TMP). The rules for TMP rarely interact with mainstream C++ programming.
- **The STL**: A very special template library. Its conventions regarding containers, iterators, algorithms, and function objects mesh beautifully (but templates and libraries can be built around other ideas too). The STL has particular ways of doing things, and when you're working with the STL, you need to be sure to follow its conventions.

Keeping all the sublanguages in mind is super-important because you will encounter situations where effective programming requires that you change strategy when you switch from one sublanguage to another. 

For example, pass-by-value is generally more efficient than pass-by-reference for built-in (C-like) types. However, when you move from the C part of C++ to the OO part of C++ the existence of user-defined constructors and destructors means that pass-by-reference-to-const is usually better. This is especially the case when working in Template C++ because you don't even know the type of object you are dealing with. When you cross into STL-land you know that iterators and function objects are modlered on pointers in C, so for iterators and function objects in the STL, the pass-by-value rule applies again.

## C++ Types

C++ does not have a fundamental base type from which all other types are derived. The language includes many built-in types. Most of the built-in types have unsigned versions.  For example, an int, which stores a 32-bit signed integer, can represent a value from -2,147,483,648 to 2,147,483,647. An unsigned int, which is also stored as 32-bits, can store a value from 0 to 4,294,967,295. 

![sizes](https://docs.microsoft.com/en-us/cpp/cpp/media/built-intypesizes.png?view=vs-2019)

C++ is a *strongly typed* language and it is also *statically typed*; every object has a type and that type never changes. When you declare a variable in your code you must define a type or use the `auto` keyword.

## Stack vs Heap Memory Allocation
Stack allocation happens on contiguous blocks of memory. It is called stack memory allocation because allocation happens in the function call stack. Whenever the function call is over, the memory for the variables is deallocated.
- Allocation and deallocation is done automatically.
- Handling of a stack frame is easier, lower accessing time
```c++
int a; 
int b[10]; 
int n = 20; 
int c[n]; 
```

Heap allocation is allocated during execution of instructions written by programmers. Distinguishable by a call to **new**.  The heap is a pile of memory space available to programmers to allocate and deallocate. Deallocating can be done by calling **delete** This is where memory leaks happen.
- Allocation and deallocation is done by the programmer
- Memory is allocated in any random order
- Handling of a heap frame is more costly, more cache misses
```c++
int* hvalue = new int;
int* harray = new int[5];
```
If later you need more memory, the program has to ask the OS for more memory (expensive!). 

## Raw Pointers

A pointer type stores the address of the location in memory where the actual data value is stored. Variables declared with `*` are referred to raw pointers and are access through `*` or `->`, also called dereferencing. Which one you use depends on whether you are dereferencing a pointer to a scalar or a pointer to a member in an object. It is no longer recommended to use raw pointers for object ownership due to evolution of the smart pointer. It is still useful and safe to use pointers for observing objects but if using them for object ownership do so with caution.

![pointer](http://joequery.me/static/images/notes/double-pointers-c/img3.png)

**Remember:** Declaring a raw pointer will allocate only the memory that is required to store an address of the memory location that the pointer will be referring to when it is dereferenced. Allocation of the memory for the data value itself (called *backing store*) is not yet allocated. Dereferncing a pointer before making sure it contains a valif address to a backing store will cause undefined behavior or usually a fatal error. 

```c++
int *pNum;
*pNum = 10;   // error: dereference uninitialized pointer
```
Corrected:
```c++
int num = 10;
int *pNum = &num;
*pNum = 41;   // num was changed, not pNum
```
Example of dereferncing:
```c++
int *p = nullptr;

int i = 5;
p = &i;
int j = *p;
```

A pointer (if it isn't declared `const`) can be incremented or decremented so that it points to a new location in memory. This is called **pointer arithmetic** and is used in C-style programming to iterate over elements in arrays or other data structures. 

On 64-bit operating systems, a pointer has a size of 64 bits; a system's pointer size determines how much addressable memory it can have. 

**Note:** When returning a pointer from a function remember that if the data it points to is a local variable, it will be destroyed when the function ends. Attempting to use that data by dereferencing the pointer will result in undefined behavior.

### Double Pointers

Double pointers operate exactly the same as single pointers far as storing a memory address. Typcially the memory address they store is that of another pointer.

![double pointers](http://joequery.me/static/images/notes/double-pointers-c/img1.png)

The double-dereference of `pp` is equivalent to `x` in the example above (`**pp == x`). Assignment, e.g. `**pp = 30` changes the value of `x`. This is equivalent to performing `x = 30`.


## RAII and Smart Pointers

Memory leaks are very common in C-style programming due to failure to call **delete** for memory that was allocated with **new**.

### **Resource Acquisition Is Initialization**

Any resource (heap memory, file handles, sockets, and so on) should be **owned** by an object that creates the newly-allocated resource in its constructor and deletes it in its destructor.

By adhering to the principle of RAII, you guarantee that all resources will be properly returned to the operating system when the owning object goes out of scope. 

```c++
class widget
{
private:
    int* data;
public:
    widget(const int size) { data = new int[size]; } // acquire
    ~widget() { delete[] data; } // release
    void do_something() {}
};
```

### **Smart Pointers**

C++ has support for easy adoption of RAII principles. A smart pointer handles the allocation and deletion of memory it owns. 

```c++
std::unique ptr;
std::shared ptr;
std::weak ptr;
```

## **STL**

The Standard Library containers all follow the principle of RAII for safe traversal of elements and are highly optimized for performance. By default use `std::vector` as the preferred sequential container in C++. This is the equivalent to `List<T>` in .NET languages.

The Standard Template Library is the part of the C++ standard library devoted to containers (e.g. vector, list, set, map, etc.) and related functionality. Much of the related functionality has to do with **function objects**: objects that act like functions. Such objects come from classes that overload `operator()`.

## **Undefined behavior**

For a variety of reasons, the behavior of some constructs in C++ is literally not defined: you can't reliably predict what will happen at runtime. 

```c++
int *p = 0;
cout << *p;

char name[] = "Reese";
char c = name[10];
```

The results of undefined behavir are not predictable and may be very unpleasant. Programs with undefined behavior can potentially erase your hard drive. It's not probable though. Most likely the program will behave erratically, sometimes running normally other times crashing, still other times producing incorrect results.

## **Interface**

Not a language element in C++. This generally talks about a function's name and signature, about the accessible elements of a class (e.g. a class's "public interface", "protected interface", or "private interface") or about the expressions that must be valid for a template's type parameter. Interfaces are a fairly general design idea.


## Naming 

Pointers are generally named with a "p" prefix. References are generally named with a "r" prefix:

```c++
Widget *pw;

class Airplane;
Airplane *pa;

class GameCharacter;
GameCharacter *pgc;

Airplane& ra;
GameCharacter& rgc;
```

## 
