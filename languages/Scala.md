Scala
=====


Basics
------

### Environment and structure

- Latest play/scala/sbt requires java 8
- To get an sbt console, just run the 'activator' command
- activator is just sbt with a couple of small UI additions in a web interface

Scala requires a top-level object like Java:

```scala
object HelloWorld {
  def main(args: Array[String]) {
    println("Hello, world! " + args.toList)
  }
}
```


### Best practices

- Don't return early, match instead
- Don't use if/else, match instead
- Use Option whenever you don't have existence guarantees
- Avoid use of 'head'; probably a bad interface or chained expression if this is required; use a proper groupby (or some other helper) instead
- Allow as few IO access inside functions as possible. Prefer to pass data in as args to keep functions pure.
- Using a mutable object is not bad in itself, it becomes bad in a functional programming environment, where you try to avoid side-effects by keeping functions pure and objects immutable. However, if you create a mutable object inside a function and modify this object, the function is still pure if you don't release a reference to this object outside the function.


### Variables

- val=final
- var=variable


### Strings vs Symbols

Symbols are interned (use a hash table to reference duplicates). Strings are not. Thus, comparing two symbols for equality is faster and saves space, but creation can be slower (need hash lookup).


```scala
// String:
var x: Int = "Hi"

//Symbol:
var y: Int = Symbol("Hi")
var z: Int = 'Hi
```


Collection types
----------------

### Overview

- scala.collection.immutable contains immutable data structs
- scala.collection.mutable contains mutable ones
- Top level of class hierarchy is Traversable (and Iterable)
- Under that is Map, Set, and Seq (self-explanatory)
- Can just Map, Set, and Seq to get a general-purpose immutable structure.
- For example, if you ask for a generic "seq", you will get a "vector", which is a general-purpose immutable structure. Check the docs for special cases and optimization.
- Option should be used instead of 'null'

### Lists

- List is immutable (mutable is called MutableList): constant time prepend, linear time append
- Thus, don't constantly append to a list!
- Either prepend to a list (which will reverse the order), or append to a mutable list, then convert to a list (constant time conversion)
- Scala's :: isn't a special type; just common w/ lists. Any time a function ends in :, it is the right side calling the function on the left.

```scala
1 :: 2 :: Nil == Nil.::(2).::(1)
```

Nil is just a shortened (idiomatic) form of List():

```scala
Nil == List()
```


Classes and OOP
---------------

### Intro

- Object = a single instantiation of an object (singleton)
- Class = a traditional class
- No such thing as 'static methods'. It's common to have a class and object w/ same name to allow for an analogue to static methods.
- Everything defaults to public. "private val name" makes private.

### "New" vs "Apply"

Use 'new' if you want the constructor to be called. If you don't use new, 'apply' is called.

For case classes, new and apply are one and the same, but this isn't always true.

Java rarely has an apply method, so you typically have to use new if working with Java classes


### Constructor parameters

```
class MyTest(w: String, private val x: String, val y: String, var z: String) {
  // Constructor space
  val doublex = x+x
  private val doubley = y+y

  // Method definition space (so the entire class body basically all gets executed during object instantiation, whether it's actual code or just function defs)
  def printAvailable(): Unit = println(w, x, y, z)
}

val myObj = new MyTest("1", "2", "3", "4")

// All variables are privately available
// w and x are identical (private val by default)
myObj.printAvailable()

// Only y and z (val, var) are publicly available
println(myObj.y, myObj.z)

// Can also access public variables defined by the constructor
println(myObj.doublex)

// Only vars can be modified w/o setters
myObj.z = "k"

// Case class vars are public
case class MyCase(v: String)
val myCase = new MyCase("v")
println(myCase.v)
```


- If no val or var is given, the variable is solely private. If val/var is used, then it sticks around as an instance variable ('field'). Technically, fields are 100% always private, but they can be accessed thru getters/setters, which can be overridden. The getters/setters are created automatically.
- `bar: Int`, `private val bar: Int`
    - If this variable is not used anywhere except the constructor, it is garbage-collected and is no longer available. Otherwise, a field is created and is available object-wide. This is never publicly available.
- `val bar: Int`
    - Same as above but getter is public
- `bar: Int` (in case classes)
    - When case classes are involved, by default each parameter has val modifier (so can be accessed externally and internally).

### Case classes

Case classes can be seen as plain and immutable data-holding objects that should exclusively depend on their constructor arguments.

This functional concept allows us to

- use a compact initialisation syntax (Node(1, Leaf(2), None)))
- decompose them using pattern matching
- have equality comparisons implicitly defined
- In combination with inheritance, case classes are used to mimic algebraic datatypes.

Note: **If an object performs stateful computations on the inside or exhibits other kinds of complex behaviour, it should be an ordinary class.**

### Traits and inheritance

- A Scala "trait" is similar to a Java interface, but can be partially implemented.

```scala
object objectName extends A with B with C
```

- The extends/with syntax doesn't mean "objectName extends A. Also, it inherits B and C". It means "objectName extends (A with B with C)". Different than Java.


Functions
---------

- def is "by name": the right-hand side is evaluated on each use.
- val is "by value": the right-hand side is evaluated upon encountering.

Final line is always the 'return', don't have to explicitly state this; no actual need for a return statement.

Functions have special syntax where the type can be inferred from the last line. Avoid this, however, as it can lead to confusion.

```scala
// Don't use
def test1() = {
    2 + 3
}
// Good
def test2(): Double = {
    2 + 3
}
// Don't use
def testa() {
    2 + 3
}
// Good
def testb(): Unit = {
    2 + 3
}
```

Takes a function which takes a double and returns a double

```scala
def test(infunct: Double => Double): Double = {
    infunct(5)
}
```

Can define functions w/ two+ sets of parens. The add2 allows for curried (partial) functions, however.

```scala
def add(a: Int)(b: Int) = { a + b }
def add2(a:Int) = { b:Int => a + b }
```

Implicit functions allow you to define implicit type conversions. The below 3 lines will automatically convert any string which is being implicitly cast to a string to the number "1". The function signature must be unique.

```scala
var X: Int = "Hi"
println(X)
implicit def StringToInt(x: String): Int = 1
```

There are also implicit parameters, which are basically normal parameters that are optional; only the last element in a parameter list can be optional. They are only "optional" if there is a implicitly marked parameter that matches in scope (and thus, you can't have 2 implicits of the same type, since it's ambiguous, similar to implicit functions):

```scala
def main(args: Array[String]): Unit = {
    implicit val j = "3"
    StringToInt("1")("2")
}
def StringToInt(a: String)(b: String)(implicit c: String, d: String): Unit = {
    println(a, b, c, d)
}
>>(1,2,3,3)
```

### Partially applied functions

Placeholders can be used to create partially applied functions variables; the placeholder fills all remaining parameters:

```scala
def printStrings(a: String)(b: String)(c: String): Unit = {
    println(a, b, c)
}

var hi = printStrings("1")(_)
hi("2")("3")
```

### Anonymous functions

The underscore is also used to define anonymous functions. As opposed to Java, anonymous functions behave as true closures; don't have to be final. Outer vars can be modified by the inner anonymous function.

```scala
_.endsWith(query)
```

is the same as the anonymous function

```scala
fileName => fileName.endsWith(query)
```


### Parametric polymorphism

Immediately following a def name, you can specify type parameters such as:

```scala
def test[A, B, C](a: List[A], c: List[C]): C = {
    c.head
}
```

This allows you to use genericized types anywhere in the rest of the function (parameters, return type, or function body).

You will get a "type not found" error if genericized types are used as an argument w/o the type variable declarations.

Type parameters also can be used on classes.

### Tail recursion

Tail recursion is an optimization, where if recursion is the last line, it doesn't use a new stack frame. @tailrec guarantees that the method is tail recursive; it will throw an error if it's not TR.


Pattern matching
----------------

Note: match/case blocks inexplicably require {} instead of (), even on single-line blocks!

Pattern matching can be thought of as a more sophisticated, extensible version of a switch statement. Can match either types or content, matching is done in order.

```scala
println(matchTest("1"))

def matchTest(x: Any): Any = x match {
    case 1 => "one"
    case "two" => 2
    case y: Int => "type match: scala.Int"
    case _ => "last chance!"
}

def patternMatcher(x: Any) = {
    x match {
        // If using 'guards' (if part of the case statement), must specify a type; this regulates what sorts of comparisons can be made (can't run > on a string)
        // 'Any' is an option, but some type must be specified
        // From there, the cases are checked in order
        case y: Int if y > 5 => y + " is int > 5"
        case y: Double if y < 5 => y + " is double < 5"
        case y: Int => y + " is int"
        case y: Double => y + " is double"
        case y: String => y + " is string"
        case y: Any => y + " is any"
        case _ => "default"
    }
}
```

Can do some interesting things with algebraic data types and pattern matching. Can recursively call different cases depending on what a given type is, allowing you to iterate through a whole algebraic data type.

```scala
abstract class Term
case class Var(name: String) extends Term
case class Fun(arg: String, body: Term) extends Term
case class App(f: Term, v: Term) extends Term
object TermTest extends scala.App {
def printTerm(term: Term) {
    term match {
        case Var(n) =>
            print(n)
        case Fun(x, b) =>
            print("^" + x + ".")
            printTerm(b)
        case App(f, v) =>
            print("(")
            printTerm(f)
            print(" ")
            printTerm(v)
            print(")")
    }
}
val t = Fun("x", Fun("y", App(Var("x"), Var("y"))))
printTerm(t)
```

The unapply method is used to create an extractor, which is used to determine what is being used to pattern match against in a class (selects one or more instance variable to match):

```scala
case class User(id: Int, name: String)
object UserA {
  def unapply(x: User) = Some(x.id)
}
val a = new User(2, "User")
a match {
  case UserA(1) => println("match")
  case UserA(2) => println("2match")
  case _ => println("nomatch")
}
```

Typing
------

### Type erasure

Because of Java VM backing, all type information is removed from generics. For example, List\[Int\] becomes just a List. Use TypeTags to get around this.

```scala
def test[A](input: List[A]) = {
  input match {
    case x: List[String] => "strings"
    case x: List[Int] => "ints"
  }
}
println(test(List(1, 2, 3)))
println(test(List("1", "2", "3")))

>>"strings"
>>"ints"
```

### Variance

One question that comes up: if T’ is a subclass of T, is Container\[T’\] considered a subclass of Container\[T\]?

On classes (not functions!), type parameters can have variance notation (a hierarchy specification):

```
[+T] = covariant = C[T’] is a subclass of C[T] (much more common)
[-T] = contravariant = C[T] is a subclass of C[T’]
[T] = C[T] and C[T’] are not related
```

Again, this is only pertinent to containers!

```
class Covariant[+A]
class Contravariant[-A]
class Invariant[A]
val cv1: Covariant[AnyRef] = new Covariant[String]
val cv2: Contravariant[String] = new Contravariant[AnyRef]

//Throws errors:
val cv3: Contravariant[AnyRef] = new Contravariant[String]
val cv4: Covariant[String] = new Covariant[AnyRef]
val cv5: Invariant[AnyRef] = new Covariant[String]
```

### Bounds

```
B >: A = lower type bound = means method will accept A or any supertype of A
B <: A = upper type bound = means method will accept A or any subtype of A (much more common)
```

- Below, testdef takes a testclass1 or any supertype of testclass1.
- Big point: what the heck? How can I pass in testclass2?! It's not a supertype of testclass1!
- No, it's not a supertype, but if you walk up the type hierarchy, both are of type "Product with Serializable".
- So, Product with Serializable is the type that is given to the function return, which allows it to compile and run.
- Summary: scala will implicitly genericize types as necessary for lower type bounds to allow for compilation.
- Since all eventually have type Any, lower type bound won't ever prevent compilation; just automatically typecasts to the "most specific common class".

```
case class testclass1(val1: Int)
case class testclass2(val1: Int)
def testdef[A >: testclass1](a: A): A = a
val res1 = testdef(testclass2(1))
```

Evaluation strategies
---------------------

Call by value and call by name determine when the inputs are evaluated: either when needed or when passed. Call by value has the advantage that it evaluates each function argument only once. Call by name has the advantage that if the argument isn't evaluated if it isn't used to calc the final value.

They are identical as long as they both contain pure functions and both evals terminate. If CBV terminates, then CBN also terminates. Not necessarily true in the reverse!

```scala
def test(x: Int, y: Int) = x * x
def test(x: => Int, y: => Int) = x * x

/*
test(2, 3) (same)
  both args call by value:
    2*2
    4
  both args call by name:
    2*2
    4

test(3+4, 8) (cbv is better, since it evaluates each arg only once)
  both args call by value:
    test(7, 8)
    7*7
    49

  both args call by name:
    (3+4)*(3+4)
    7*(3+4)
    7*7
    49

test(7, 2*4) (cbn is better, since y isn't used, and is only evaluated when needed)
  both args call by value:
    test(7, 8)
    7*7
    49

  both args call by name:
    7*7
    49 
*/
```

Declarative programming
-----------------------

Note: with any of these functions, prefer using the 'case' statement to name the inputs, rather than using a bunch of _._1/_._2 statements; it becomes very difficult to understand otherwise. Also, avoid heavy nesting and chaining of functions, as this becomes confusing and hard to reuse. Just break out into variables.

### Map

```scala
for {
  x <- List(1, 2)
}
yield(x + 2)
Is the same as:
List(1, 2) map {x => x + 2}
Map just assigns each variable to x, runs the function, and returns that element w/ the returned value
```

### Filter

Filters out all elements besides the ones that match the given conditional

```scala
list.filter(x=>x.account===account)
```

### Filter + map ('duh', but common)

```scala
list.filter(x=>x.account===account).map(x=>x.name)
```

### Folding (a.k.a, reduce)

"Folding" recursively processes a data structure's constituent parts, building up a return value. foldleft function common on scala data structures:

```scala
def foldLeft[B](z: B)(f: (B, A) => B): B
```

Takes a starting value (z) and a function (f). A is the list's type. Each element in the list is passed into the function as 'a', and the previous iteration's calculated is passed in as 'b'.

For example:

```scala
val lst = List(1, 2, 3, 4)
lst.foldLeft(0)((b, a) => b*2 + a)
iter1: b=0, a=1, result=0*2=1
iter2: b=1, a=2, result=1*2+2=4
iter3: b=4, a=3, result=4*2+3=11
iter4: b=11, a=4, result=11*2+3=26
```

### Flatmap

flatMap is similar to a map, but flattens the structure if it contains nested collections:

```scala
val lst = List(1, 2, 3, 4)
def f(x: Int) = List(x - 1, x, x + 1)
println(lst.map(x => f(x)))
println(lst.flatMap(x => f(x)))

prints:
>>List(List(0, 1, 2), List(1, 2, 3), List(2, 3, 4), List(3, 4, 5))
>>List(0, 1, 2, 1, 2, 3, 2, 3, 4, 3, 4, 5)
```

### Groupby

- list.groupby returns a dictionary/hash, where each key points to a sublist, grouped by the given key

### Existence and all

- collection.exists(booleanStatement)
- collection.forall(booleanStatement)
- Returns true/false depending on if either one (exists) or all (forall) of the elements match the boolean statement. Easier to write than a foldleft.

### Flatten

collection\[Option\[Type\]\].flatten returns collection\[Type\], where all of the "None"s are removed.

### Others

- foreach
- filter
- zip
- partition
- find
- drop and dropWhile


Play framework
--------------

- Start the local server with activator ~run. (w/ the tilde), or use IntelliJ's internal server (run play app, enable auto-reload).
- When starting a new project, just open as an SBT project and auto-import libs.
- Get out of database logic ASAP! Database logic w/ foreign keys is "backwards"; turn into more native objects early on to make this easier.
- Prefer a single big join operation over a bunch of small ones; many small database requests are slower than one larger one.
- Not sure if this is an actual rule, but keep your DB accesses as high as possible. If you allow a low-level DB access, chances are higher that this gets looped in some manner.

### To access h2 database browser

- At first, enter the Play console by running the Typesafe Activator:

```
~/Projects/play-app $ activator
```

- Start the web console:

```
[play-app] $ h2-browser
```

- Run the Play app:

```
[play-app] $ run
```

- Access the Play app in your browser. This will cause the Play application to connect to the in-memory H2 database and to initialize it with some default data, if any. Log in to the H2 web console in your browser to inspect the database content. Use the following settings which you can save (for instance, as Play-App In-Memory Database) to easily access them again later:

```
Driver Class: org.h2.Driver
JDBC URL: jdbc:h2:mem:play
User Name: sa
Password: <blank>
```

### Nested DB calls

Avoid nested DB calls like the plague. Looping over database access completely murders performance. One big call is way more efficient. Also, getting data as early as possible is way easier to test. Get as little data as you absolutely need.


### Slick


Build the sql query (joins/selects, etc), and then convert this to a scala list ASAP. Query doesn't execute until then, and is easy to forget.
