Object-oriented programming
===========================

Terminology
-----------

- Extends - inherit a parent class. Abstract class can't be instantiated - need a concrete class which extends it.
- Implements - adheres to a public interface. Generally preferred, as you can do your own internal implementation.
- Member variable == variable associated with a class (either static or non-static)
- Field variable == instance variable == non-static member variable
- Class variable == static member variable

OOP Best practices
------------------

### Constructors

- Constructor logic should not be super long; this leads to tight coupling.
- They should be almost entirely mere setters of the parameters.
- Instantiating objects in the constructor can be a problem, since the objects are coupled in the constructor

### Class best practices

- All classes should have as small a public interface as possible.
- All non-constant fields should have private access.
- Any alterations to the values of fields should be via method calls.

### When to refactor functions

- If you have a lot of 'connecting' functions (functions which frequently require the same two arguments), consider creating a class which wraps the two arguments, with the functions as class methods
- Deeply nested control structures: e.g. for-loops 3 levels deep or even just 2 levels deep with nested if-statements that have complex conditions.
- Too many state-defining parameters, e.g., function parameters that guarantee a particular execution path through the function. Get too many of these type of parameters and you have a combinatorial explosion of execution paths.
- Logic that is duplicated in other methods: poor code re-use is a huge contributor to monolithic procedural code.
- Excessive coupling between classes.
- Your massive developer-grade display isn't big enough to display it.
- You can't immediately determine the function's purpose. If you can't summarize this purpose in a single sentence or happen to have a tremendous headache


Interfaces and inheritance
--------------------------

Interfaces do not exist (specifically in Java) to support multiple inheritance. That's kind of a hack people occasionally use them for, but their real purpose is to define a contract that classes support to facilitate polymorphism.

Prefer using interfaces (abstract base classes" in Python) over inheritance; hierarchical inheritance can get really messy.

### Naming

"_able" adjective if interface named after things that can be done to the object (e.g. Enumerable), a "_er" noun if interface named after things your object does to other objects (e.g. EqualityComparer), noun if your interface is named after a thing whose behavior it mimics (e.g. List<T>)


Inversion of control
--------------------

### Non-IOC Programming

You will instantiate ObjectB and ObjectC within Class A by calling constructors of B and C somewhere within Class A. That means creation of ObjectB and ObjectC depends on programming.

```java
Class A{
    B b;
    C c;
    /*
    See how b and c are instantiated by calling constructor of class B and C.
    That means, Class A needs knowledge about B and C like
    ‘Which are the possible constructors of B and C?
    What should be the constructor parameters?’
    This is called as Tight coupling between A and B/A and C.
    */
    Public A(){
        b = new B();
        c = new C();
    }
}
```

### IOC Programming

Instantiation of ObjectB and ObjectC will be done by container and will be provided to Class A directly. You can either provide created object as a Constructor Argument (Constructor Injection) or using Setter Method (Setter Injection). Results in loose coupling, reusable components and easy to configure application.

In summary, you should think twice and hard about having "new" in a constructor. Prefer to set instance variables as arguments or setters.

Also consider: you might want to group commonly used combination of parameters into factories. For example, in class Class(A a, B b, C c), where A B and C are interfaces, if you use, say Class(new AConcrete1(), new BConcrete3(), new CConcrete4()) at some point (_especially_ if you do this multiple times), do this in a factory instead of copying that same code in each time it's used. If you have to change the concrete objects being used for that situation, then you only have to change the passed parameters in one place.

```java
Class A{
    B b;
    C c;
    /*
    Look Object of class B and C will be created by Container and provided as Constructor argument.
    So, Class A doesn’t need to know class B and C constructors and all.
    It is loose coupling between A and B/A and C.
    */
    Public A(B b, C c){
        this.b = b;
        this.c = c;
    }
}
```
