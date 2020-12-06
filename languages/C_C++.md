C/C++
=====

Data types
----------

- std::map
- std::vector (vector is array implementation, list is pointer implementation; typically prefer vector for general purpose)
- std::set


Syntax/general notes
------------

### Typedeffing

Using something like `typedef struct usb_dev_handle usb_dev_handle` in a library declares the struct, but doesn't actually define it. This is used in situations where a library is trying to completely hide the implementation details within the header.


### Callbacks

Member functions aren't allowed, since the caller would need the full object+state.


### Common naming scheme

- mFoo = member variable
- pFoo = pointer


### Incrementing

Pre-increment ++i increments the value of i and evaluates to the new incremented value. Post-increment i++ increments the value of i and evaluates to the original non-incremented value. Probably generally better to do this explicitly for clarity's sake.


### Volatile variables

- volatile is (nearly) useless for platform-agnostic, multithreaded application programming. It does not provide any synchronization, it does not create memory fences, nor does it ensure the order of execution of operations. It does not make operations atomic. It does not make your code magically thread safe.
- Volatile was specifically intended to be used when interfacing with memory-mapped hardware, signal handlers and the setjmp machine code instruction. One use is to prevent an optimizing compiler from removing a chunk of code where it assumes a variable will never change (but actually can due to I/O mapped nature).


### Headers

- Headers should declare all public namespaces, classes, and functions contained within them. Contain no actual logic, however. Don't include private (non-public) data in a header (unless C++, where classes have identifiers for this).
- Headers should only have includes which are required for the header itself; rest of the includes should be in the source file.

### Stack variables vs heap

- You don't \*necessarily\* need to know a stack-allocated piece of data's size at compile time. Newer implementations of C++ don't require this, although some still do.
- Hence, you \*may\* be able to do something like `char charArray\[somevar + 50\]` and it will work without issue, and will be on the stack. The compiler knows best.

### Object allocation and lifetime

- `new` should be used as little as possible in C++; use a wrapper function which allocates (and deallocates!) data automatically for you! <http://stackoverflow.com/questions/6500313/why-should-new-be-used-as-little-as-possible>
- The 100% only time that you will ever need to use `free` is on `malloc`/`calloc`/`realloc`'ed data.


Object-oriented programming
---------------------------

### Scope

Double colon (::) - selects what class's scope you are targeting.

### Inheritance

Inheritance with : during class declaration. Inherit protected and public variables. Has an access specifier which declares how the variables/functions are incorporated into the new derived class.

Multiple inheritance is allowed (but probably use interfaces instead): `class CRectangle: public CPolygon, public COutput{ }`


### Overloading

Basic operators are overloadable. Can create a 'vector class' where you would 'add' two classes together. Would normally fail unless you overload this w/ 'operator' designation.


### Struct vs class

In a class definition, the default access for members and base classes is private. In a struct definition, the default is public. That is the only difference between a class and a struct, although by convention, some programmers use struct only for POD (plain old data) classes and use class for all other classes.


### Static variables and functions

- Normally `static` means that a variable has static duration - is allocated at the beginning of the program and exists for the whole duration. Thus, variables are shared between all object instances of a class ('class variable'). Static functions can be called them without instantiating an object ('instance method' vs 'class method').
- C has no objects, thus `static` has a different meaning: the function or variable is only accessible via functions inside the same source file, comparable to private functions and members in C++, Java and C\#.


### Templates

- Allows classes and interfaces to be parameters which are also passed in during object instantiation
- List<String> list = new ArrayList<String>();
- Creates a list with the type String being passed in (that string type can be used in the class's implementation, similar to an argument). This is still a List, it just has an internal structure of String being used.
- This adds some additional compile-time type checking, and may also save you some casting

### Constructor

Constructor gets run when the object is created. Destructor is called when the object is deleted.

Common to only specify the class variables and methods in the class definition, and then fill in the actual meat further down:

```cpp
class CRectangle {
    int *width, *height;
  public:
    CRectangle (int,int);
    ~CRectangle ();
    int area () {return (*width * *height);}
};

CRectangle::CRectangle (int a, int b) {
  width = new int;
  height = new int;
  *width = a;
  *height = b;
}
```

`Classname::Classname` is a constructor, `Classname::~Classname` is a destructor.

Colon after constructor is an 'initialization list'. By default, when you initialize an inherited class, all inherited constructors get called, then finally the class's constructor is called. If you don't want this, you use an initialization list ( a : after the constructor) to specify the inputs to the constructor. Initialization lists can also initialize variables.

Default constructor = the constructor with no arguments.


Pointers and references
-----------------------

- When declaring variables, a reference is effectively just an alias for a variable name that you can't do pointer arithmetic on. In C++, only use pointers when you plan on using pointer arithmetic; otherwise use a reference.
    - Pointer: `*`
    - Reference: `&`
- Pointer manipulation
    - `&`: "Address of"
    - `*`: "Value pointed by"
    - void\* = generic pointer: can later cast to whatever necessary data type
- Pass by value:
    - `funct(Objecttype obj)`
- Pass by reference
    - `funct(Objecttype \*obj)`
    - `funct(Objecttype &obj)`
    - The pointer (`*`) will have to be de-reffed (`*`) to access the member functions or variables. The reference (`&`) is automatically de-reffed, and can be used as if you are directly manipulating the object, even though you are just manipulating a reference to the object.
- References can't change value.
- References don't exist in C.



C vs C++
--------

- C uses <stdio.h>, the C++ equivalent library is <cstdio>. Even better, <iostream> enables `cout <<` style-print statments, which are generally easier to work with.
- C uses malloc/free, C++ uses new/delete
- Since C++ has overloading of function names and C does not, the C++ compiler cannot just use the function name as a unique id to link to, so it mangles the name by adding information about the arguments. A C compiler does not need to mangle the name since you can not overload function names in C. When you state that a function has extern "C" linkage in C++, the C++ compiler does not add argument/parameter type information to the name used for linkage.
- C++ has:
    - Namespaces
    - Class encapsulation
    - Templates
    - Streams
    - Inheritance
    - Virtual methods
    - Overloaded operators
    - Reference passing (& in the function arguments)
    - Exceptions


Windows C/C++/C# Flavors
------------------------

- C\# and Visual Basic are the two primary .NET languages. There are also languages such as C++/CLI (CLR) and J\#, but they are not as commonly used. Just a side note, C++/CLI (CLR) is different than simple C++; C++ runs native (non-bytecode) programs, while C++/CLI (CLR) is first translated into .NET bytecode, and then finally run on the .NET virtual machine. All of these languages are the standard within Visual Studio.
- MFC is a C++ wrapper for the Win32 API. This is starting to age, and is falling out of support by Microsoft.
- Use Qt if you need to use C++ and/or need cross-platform.
- WPF is a C\# (.NET) library - currently the most state-of-the-art GUI library supported by microsoft. Does need .NET, though, so don't use if the .NET requirement is a problem.
- MFC is a framework for creating GUI applications in native (unmanaged) C++ like the C++ you learned in college. The .NET Framework supports writing managed code in C++/CLI, C\#, VB.NET or a number of other languages. There are two ways of building a desktop GUI application on the .NET Framework - Windows Forms and WPF. Windows Forms is older and no longer under active development. WPF is newer.
- If you know C++ and intend to deploy to machines without the .NET Framework, MFC is a good choice. It creates modern-looking UIs and supports Windows 7 features. If you're willing to learn C\# and deploy to machines with the .NET Framework, consider WPF or Windows Forms. For your basic "drag on a button, double-click to edit the handler, type some code in the click handler" apps there is no difference between them. WPF has better support for graphical coolness (gradients, transparency) and for multi-touch, Windows 7 goodies etc. Some people find it harder to learn, but I think that's just because they learned Windows Forms ages ago. If you're starting from nothing you could learn either and might as well learn the newer one.


Libraries
---------

A library is a package of code that is meant to be reused by many programs. Typically, a C++ library comes in two pieces:

- A header file that defines the functionality the library is exposing (offering) to the programs using it.
- A precompiled binary that contains the implementation of that functionality pre-compiled into machine language.

Libraries are precompiled for several reasons. First, since libraries rarely change, they do not need to be recompiled often. It would be a waste of time to recompile the library every time you wrote a program that used them. Second, because precompiled objects are in machine language, it prevents people from accessing or changing the source code, which is important to businesses or people who don’t want to make their source code available for intellectual property reasons.

There are two types of libraries: static libraries and dynamic libraries.

A static library (also known as an archive) consists of routines that are compiled and linked directly into your program. When you compile a program that uses a static library, all the functionality of the static library becomes part of your executable. On Windows, static libraries typically have a .lib extension, whereas on linux, static libraries typically have an .a (archive) extension. One advantage of static libraries is that you only have to distribute the executable in order for users to run your program. Because the library becomes part of your program, this ensures that the right version of the library is always used with your program. Also, because static libraries become part of your program, you can use them just like functionality you’ve written for your own program. On the downside, because a copy of the library becomes part of every executable that uses it, this can cause a lot of wasted space. Static libraries also can not be upgraded easy — to update the library, the entire executable needs to be replaced.

A dynamic library (also called a shared library) consists of routines that are loaded into your application at run time. When you compile a program that uses a dynamic library, the library does not become part of your executable — it remains as a separate unit. On Windows, dynamic libraries typically have a .dll (dynamic link library) extension, whereas on Linux, dynamic libraries typically have a .so (shared object) extension. One advantage of dynamic libraries is that many programs can share one copy, which saves space. Perhaps a bigger advantage is that the dynamic library can be upgraded to a newer version without replacing all of the executables that use it.

Because dynamic libraries are not linked into your program, programs using dynamic libraries must explicitly load and interface with the dynamic library. This mechanisms can be confusing, and makes interfacing with a dynamic library awkward. To make dynamic libraries easier to use, an import library can be used.

An import library is a library that automates the process of loading and using a dynamic library. On Windows, this is typically done via a small static library (.lib) of the same name as the dynamic library (.dll). The static library is linked into the program at compile time, and then the functionality of the dynamic library can effectively be used as if it were a static library. On Linux, the shared object (.so) file doubles as both a dynamic library and an import library. When using this implicit loading of dynamic libraries, you must a) distribute the .dll with the program and b) tell the linker to link this library.

If you don't have a .lib file, or want control over when the .dll is loaded, you can use explicit loading. This is done with the LoadLibrary function, which loads the .dll into memory. You also must use the GetProcAddress() to determine the memory location of the function which you intend to use.

Windows, by default, will look for .dll files in the application's current directory, as well as the path variable. To inspect what is contained within a .dll file on Windows, use the dumpbin utility. To see what dlls are being loaded in Windows, use the process explorer (sysinternals).


Qt
---

### Qobjects

Normally, C++ prefers not using 'new' unless necessary. However, Qt uses a hierarchy where a Qobject parent is given in the constructor for a child Qobject. When the parent is deallocated, the children are also deallocated.

### "Application has failed to initialize"

Check that required .dll's have execute permissions.
