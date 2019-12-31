---
tags:
  - CPP
  - C++
  - Effective
  - Definitions
---

# C++ Definitions

## **declaration** 

tells compilers about the name and type of something. 

```
extern int x;  						  // object declaration
std::size_t numDigits(int num); 	  // function declaration
class Widget;					      // class declaration
template<typename T> 				  // template delcaration
class GraphNode; 
```

A function's declaration reveals its **signature**, i.e., its parameter and return types. A function's signature is the same as its type. In the case of numDigits, the signature is `std::size_t (int)`, i.e., "the function taking an int and returning a std::size_t".

## **definition**

provides compilers with details about the entity a declaration omits. For an object, the definition is where compilers set aside memory for the object.

```
int x; 								// object definition

std::size_t numDigits(int number)	// function definition
{
	std::size_t digitsSoFar = 1;
	while ((number /= 10) != 0) ++digitsSoFar;
	return digitsSoFar;
}

class Widget { 						// class definition
public:
	Widgit();
	~Widgit();
};

template<typename T>				// template definition
class GraphNode {
public:
	GraphNode();
	~GraphNode();
};
```

## **initialization**

the process of giving an object its first value. For objects generated from structs or classes, initialization is performed by constructors. a **default constructor** is one that can be called without any arguments.

```
class A {
public:
	A();
};
```

The **explicit** keyword prevents the constructors from being used to perform implicit type conversions. For example,

`This compiles:`
```
class B {
public:
	B(int x = 0, bool b = true)			 // default constructor
    {}	
};

void doSomething(B b) 
{}

int main() {
    doSomething(20);
    return 0;
}
```
`But this does not:`
```
class B {
public:
	explicit B(int x = 0, bool b = true) // default constructor
    {}	
};

void doSomething(B b) 
{}

int main() {
    doSomething(20); // error: could not convert '20' from 'int' to 'B'
    return 0;
}
```
The **copy constructor** is used to initialize an object with a different object of the same type. The **copy assignment operator** is used to copy the value from one object to another of the same type.

```
class Widget {
public: 
	Widget();							  // default constructor
	Widget(const Widget& rhs);			  // copy constructor
	Widget& operator=(const Widget& rhs); // copy assignment operator
}
Widget w1;								  // invoke default constructor
Widget w2(w1);							  // invoke copy constructor
Widget w2 = w1;							  // invoke copy constructor
w1 = w2;								  // invoke copy assignment operator
```

Fortunately, copy construction is easy to distinguish from copy assignment. If a new object is being defined, a constructor has to be called.

A copy assignment operator of class T is a non-template non-static member function with the name `operator=` that takes one parameter of type `T`, `T&`, `const T&`, `volatile T&`, or `volatile const T&`. The copy assignment operator is called whenever selected by overload resolution, e.g. when an object appears on the left side of an assignment expression.

```
bool hasAcceptableQuality(Widget w);   // w is passed to the function by value

Widget aWidget;
if (hasAcceptableQuality(aWidget))... 
```

In the call above, `aWidget` is copied into `w`. The copying is done by Widget's copy constructor. **Pass-by-value** means "call the copy constructor". Note: it is generally a bad choice to pass user-defined types by value. Pass-by-reference-to-const is typically a better choice.

## **STL**

The Standard Template Library is the part of the C++ standard library devoted to containers (e.g. vector, list, set, map, etc.) and related functionality. Much of the related functionality has to do with **function objects**: objects that act like functions. Such objects come from classes that overload `operator()`.

## **Undefined behavior**

For a variety of reasons, the behavior of some constructs in C++ is literally not defined: you can't reliably predict what will happen at runtime. 

```
int *p = 0;
cout << *p;

char name[] = "Reese";
char c = name[10];
```

The results of undefined behavir are not predictable and may be very unpleasant. Programs with undefined behavior can potentially erase your hard drive. It's not probable though. Most likely the program will behave erratically, sometimes running normally other times crashing, still other times producing incorrect results.

## **interface**

Not a language element in C++. This generally talks about a function's name and signature, about the accessible elements of a class (e.g. a class's "public interface", "protected interface", or "private interface") or about the expressions that must be valid for a template's type parameter. Interfaces are a fairly general design idea.

