---
tags:
  - CPP
  - C++
  - Features
---

# General C++ Notes

## Welcome back to C++
C++ is one of the most widely used programming languages in the world. Well-written programs are fast and efficient, the language is more flexible than other languages because it enables you to access low-level hardware features to maximize speed and minimize memory requirements.

Backwards compatibility with C was one of the original requirements for C++. The evolution of C++ features greatly reduce the need to use C-style idioms. 

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
