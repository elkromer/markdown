---
tags:
  - cs
  - csharp
  - delegates
---

# Delegates Introduction

Delegate is a type that encapsulates both an object instance and a method. Similar to C++ function pointers but are fully object-oriented. They are used extensively to define callback methods and can be chained together. Below is a delegate **declaration**

```cs
public delegate void Del(string message);
```

Delegate types are derived from the **Delegate** class in .NET. Delegate types are sealed-- they cannot be derived from. A delegate can wrap a method defined somewhere else, like this one

```cs
public static void DelegateMethod(string message)
{
    Console.WriteLine(message);
}
```

A delegate object is constructed by setting the method the delegate will wrap

```cs
Del handler = DelegateMethod;
```
Because the instantiated delegate is an object, it can be passed as a parameter or assigned to a property. This is an asynchronous callback. When a delegate is constructed to wrap an instance-method, the delegate references both the instance and the method. 

### Multicasting

A delegate can call more than one method when invoked. To add an extra method to the delegate's list of methods (the **invocation list**), add delegates using the addition operators.

```cs
var obj = new MethodClass();
Del d1 = obj.Method1;
Del d2 = obj.Method2;
Del d3 = DelegateMethod;

//Both types of assignment are valid.
Del allMethodsDelegate = d1 + d2;
allMethodsDelegate += d3;
```

At this point, `allMethodsDelegate` contains three methods in its invocation list (`Method1`, `Method2`, and `DelegateMethod`). The original three delegates remain unchanged. When `allMethodsDelegate` is invoked, all three methods are called in order. If the delegate uses reference parameters, the reference is passed sequentially to each of the three methods in turn, and any changes by one method are visible to the next method. When any of the methods throws an exception that is not caught within the method, that exception is passed to the caller of the delegate

Multicast delegates are used extensively in event handling. Event source objects send event notifications to recipient objects that have registered to receive that event. To register for an event, the recipient creates a method designed to handle the event, then creates a delegate for that method and passes the delegate to the event source. The source calls the delegate when the event occurs. The delegate then calls the event handling method on the recipient, delivering the data. The delegate type for a given event is defined by the event source

### When to use delegates instead of interfaces

Both delegates and interfaces enable a programmer to separate type declarations and implementation. An interface can be inherited and implemented by **any** class or struct. A delegate can be created for a method on **any** class, as long as the method fits the method signature for the delegate. An interface reference or a delegate can be used by an object that has no knowledge of the class that implements the interface or delegate method.

Use a delegate in the following circumstances:
  - Event driven design pattern
  - It is desirable to encapsulate a static method
  - The caller has no need to access other properties, methods, or interfaces on the object implementing the method
  - Easy composition is desired
  - A class may need more than one implementation of the method

Use an interface in the following circumstances:
  - There is agroup of related methods that may be called
  - A class only needs one implementation of the method
  - The class using the interface will want to cast that interface to other interface or class types
  - The method being implemented is linked to the type or identity of the class

