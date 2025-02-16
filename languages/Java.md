Java
====

Data structures
---------------

- List - abstract list
    - ArrayList - concrete list implemented as an array
- Map - abstract map
    - Hashmap - map using hash table
- Set - set interface

When creating an object which is inherited from an abstract type (e.g., Map), make sure to initialize using the abstract class, instantiate (w/ 'new') with the concrete type (e.g., HashMap). This allows the flexibility of using, for example, a Map as a parameter, but being able to pass either a TreeMap or a HashMap in. Always use the highest-level interface possible.

Watch out for terms like "returns a view" or "backed by". For instance, the sublist function, which returns part of an array, doesn't return a copy of part of the array, it returns a "view" of part of the array. This is faster, but watch for mutability/concurrency issues.


Program structure
-----------------

### Classpaths

- Classpaths specify what libraries are available at runtime
    - Easiest just to append to the system/user variable "CLASSPATH", as in CLASSPATH = "C:\\Program Files (x86)\\Weka-3-7\\\*"
    - Can also use at the command line like "java -cp "Test.jar;lib/\*" my.package.MainClass"

### File types

- `.jar`:  library
- `.class`: virtual machine compiled code (java bytecode)
- `.java`:  source code


Java streams and readers
------------------------

- Stream = flow of data
- Byte stream is lowest level
- Can use a filereader to analyze character-by-character
- Buffered reader allows line-by-line access (takes filereader as input). It also buffers the reads so that each read or readline doesn't cause a read to/from disk.
- Can then wrap a scanner around a buffered reader to look for individual tokens (can use scanner.useDelimiter)
- Verbose summary:
    - A stream is a way of sequentially accessing a file. A byte stream access the file byte by byte. A byte stream is suitable for any kind of file, however not quite appropriate for text files. For example, if the file is using a unicode encoding and a character is represented with two bytes, the byte stream will treat these separately and you will need to do the conversion yourself.
    - A character stream will read a file character by character. A character stream needs to be given the file's encoding in order to work properly.


Iterating through hashes
------------------------

```java
// Just the keys:
for (String key : map.keySet()) {
    // ...
}

// Just the values:
for (Object value : map.values()) {
    // ...
}

// Keys and values:
for (Map.Entry<String, Object> entry : map.entrySet()) {
    String key = entry.getKey();
    Object value = entry.getValue();
    // ...
}
```

