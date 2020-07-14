---
tags:
  - Java
---

### Core Concepts

**Polymorphism**

It refers to a **principle in biology** in which an organism or species can have many different forms or stages. This principle can also be applied to object-oriented programming and languages like Java. 
* If a class can pass more than one "is a" test, it is considered to be polymorphic.

The principle deals with how your program decides which methods it should use depending on what type of `thing` it has. 

**Inheritance**

A concrete way to form new classes based on existing classes, taking on their attributes/behavior. "HTTP is an IPPORT". Inheritance results in polymorphism.

### Java Desktop Applications

### Packaging a JAR
An artifact is an assembly which someone put together. The term "artifact" has also become kind of a colloquial term for "that jar" but it specifically refers to compiled Java classes, a Java application packaged in a Java archive, a Web application directory structure, or a Web application archive. 

An artifact can be just an archive file or a directory structure. In the case of a directory structure, it can have the following structural elements within it:
- Compilation output
- Libraries 
- Collections of resources 
- Other artifacts

In IntelliJ IDEA, go to Project Structure > Artifacts. Click + and add a new JAR artifact. Depending on the type of jar you want to create, either just create an empty jar or a jar based on a module with dependencies. Choose option 2 if you are writing a Java console application.

