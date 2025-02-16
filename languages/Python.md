Python
======

Collections
-----------

### Common types

- Tuple - ("elem1", "elem2") - immutable
- int, floast, str, bytes - immutable
- Dict - {"key": "value} - mutable
- List - \["elem1", "elem2"\] - mutable
- Set - set(\['a', 'b', 'c', 'c', 'd'\]) - mutable

### Grouping

Allows you to iterate upon group changes. Groups must be sorted, or they will change w/ every key change and not group properly.

```python
for k, g in groupby(data, keyfunc):
    for item in group:
        print(key, group)
```

### Slicing

```python
a[start:end] # items start through end-1
a[start:]    # items start through the rest of the array
a[:end]      # items from the beginning through end-1
a[start:end:step] # start through not past end, by step
a[-1]    # last item in the array
a[-2:]   # last two items in the array
a[:-2]   # everything except the last two items
```

### List comprehensions


```python
# Creates a list from an iterable in one line:
[str(x) for x in my_list if x.a==x.b]

# Nested list comp; z is returned in each iteration in this case
[z for b in a for c in b for d in c ... for z in y]

# Is the same as
for b in a:
    for c in b:
        for d in c:
            ...
                for z in y:
```

### Zip

To combine two iterables, for example, if you have array A and B, and you want to go through them both together (without having to keep an index around), use the following. Note, this is truncated to the shorter of the two.

```python
for i, j in zip( range(5), range(1,20,2) ):
    print i, j
0 1
1 3
2 5
3 7
4 9
```

### Chain

To chain two generators together

```python
import itertools
for i in itertools.chain(range(5),range(1,20,2)):
    print (i)
0
1
2
3
4
1
3
5
7
9
11
13
15
17
19
```

### Concatenate

Can also just concatenate if they are lists (doesn't work on generators)

```python
for i in [1,2,3,4,5]+[3,5,7,9,11,13,15,17,19]:
    print (i)
0
1
2
3
4
1
3
5
7
9
11
13
15
17
19
```


### Overhead

To analyze the amount of overhead that python data structures has, I wrote a 2D set of random numbers to various data structures and analyzed their memory usage:

- tuple: 91.1MB
- named tuple: 91.5MB (but be careful, creating these will leave a reference to the factory laying around as well, so don't leave the factory in a loop!)
- list: 105.1MB
- dict: 194.5MB
- class: 233.9MB
- By default, python uses a dictionary to identify data in each and every object. This is very memory intensive. Using slots allows you to statically define the attributes inside of an object, which removes the need for a dictionary in each object.
- Slots are extremely memory efficient and simple to use


Unicode
-------

### Python 2v3 unicode types

- Python 2
    - str = "" = just a bunch of bytes; could have been encoded using any codec
    - unicode = u"" = a unicode representation of text
- Python 3
    - str = b"" = just a bunch of bytes; could have been encoded using any codec
    - unicode = "" = a unicode representation of text
- u"\\u263a" = unicode hex code (only works in a unicode object, not a str!)
- unichr(9786) = unicode decimal code
- Decode = bytes => text
- Encode = text => bytes

### Dealing with unicode

Python 2's unicode support is tricky (as is unicode in general).

What this basically amounts to is that Python 2 uses ASCII as the default encoding for byte strings. Hence, if you ever try to encode something (without specifying the encoding as UTF-8) in Python, it will use ASCII by default. This is perfectly fine, unless it encounters something which is beyond the ASCII range of unicode (e.g., foreign characters or emojis). This is unfortunately not always easily detectable; your code could run fine as long as it is receiving ASCII characters, but will crash as soon as a non-ASCII character is encountered. Note, if you are 100% sure that you will never encounter any unicode (e.g., you're working on closed-loop internal code), you can still use str and format to your heart's content. If you are dealing with external data, however, this should definitely be considered.

More detail is below, but the gist of this is:

- Don't use any of the \`str\` object functions.
- Use u"" instead of "".
- As a rule of thumb, decode early, use unicode wherever possible, and encode late.


```python
unicode_char = unichr(1024)

# Can't write unicode w/ ASCII encoding
handle = open("out.txt", 'w')
handle.write(unicode_char)
handle.close()

# Can't convert unicode to ASCII-encoded bytestring
str(unicode_char)

# Can't format ASCII w/ unicode variable
"{}".format(unicode_char)

#
# Here's how you can solve these problems:
#

# Use the codecs module to write utf-8
import codecs
handle = codecs.open("out.txt", 'w', 'utf-8')
handle.write(unicode_char)
handle.close()

# Alternatively, encode your strings explicitly
handle = open("out.txt", 'w')
handle.write(unicode_char.encode('utf-8'))
handle.close()

# Don't use str(), encode explicitly
unicode_char.encode('utf-8')

# Use unicode object to format, not str literal
print(u"{}".format(unicode_char))

# Note, the above examples use unichr(1024), which is of type `unicode`.
# If you have an un-decoded str in the unicode range that you are using as an input to the format function (as below), it will throw an error.
# This is because the system attempts to decode it with the default codec, ASCII, which is impossible.
print(u"{}".format("\xe2\x80\xa6"))

# To fix this, you must fist decode the string to a unicode object, rather than a bytestring.
print(u"{}".format("\xe2\x80\xa6".decode('utf-8')))

# As a general rule, decode all strs to a unicode object ASAP.
```


Context manager
---------------

A class w/ `__enter__()` and `__exit__()` can be used with a "with" directive to run code when the block starts/stops. One example of this is when opening files; cleanup happens automatically when the block completes:

```python
with open('workfile', 'r') as f:
    read_data = f.read()
```


Equality and comparisons
------------------------


### is vs ==

'is' is identity testing, '==' is equality testing.

```python
a = 'pub'
b = "".join(['p', 'u', 'b'])
a == b
>>True
a is b
>>False
```

"is" comparison works for strings because they are interned (stored in a hash map w/ a pointer). This is actually faster than '==', since the actual string content doesn't need to be compared; you only need to compare the pointers of the two objects.


### Check "memory location"

```python
id(variable)
```

This gives you a unique identifier to that variable. Useful for checking if you are using a copy of or the original object.

### Boolean equivalency

```python
# Although [] doesn't equal False, it converts to False if you use it in a boolean expression
# This is useful for using them in if statements
[] == False
>> False
bool([]) == False
>> True
```

The boolean equivalent of all of these also are also False:

- None
- False
- zero of any numeric type, for example, 0, 0.0, 0j.
- any empty sequence, for example, '', (), [].
- any empty mapping, for example, {}.
- instances of user-defined classes, if the class defines a __bool__() or __len__() method, when that method returns the integer zero or bool value False. [1]



Exceptions
----------

As a rule, always use exceptions instead of returning error codes.

AFAIK, KeyboardInterrupt isn't a subclass of Exception.


### Define new exception

```python
class MyException(Exception):
    pass
```

### Raise exception w/ description

```python
raise Exception("My hovercraft is full of eels")
```

### Get exception description

```python
try:
    raise MyException({"message":"My hovercraft is full of animals", "animal":"eels"})
except MyException as e:
    details = e.args[0]
```

### Print stack trace

```python
# Inside an exception
try:
    raise TypeError("Oops")
except Exception:
    import traceback
    traceback.print_stack()

# Outside an exception
import traceback
print(''.join(traceback.format_stack()))
```


Types
-----

### Check if a string is a number

```python
def is_number(s):
    try:
        float(s)
        return True
    except ValueError:
        return False
```

### Immutable/mutable types

- Immutable types: numbers, strings, tuples, frozensets
- Mutable types: list, dict, set


Classes
-------

### Class vs instance vars

```python
class MyClass(object):
    class_var1 = "class var"
    def __init__(self, x, y):
        self.inst_var = x
        MyClass.class_var2 = y

inst = MyClass()

#This will create a instance variable w/ a conflicting name, not a class variable
class_inst.class_var1 = ""

#This will change the class variable
MyClass.class_var1 = ""
```

- Setting is always consistent. self.var will always create an instance variable, while Class.var will always create a class variable.
- Getting is not consistent; if you run print(self.var), it will first check for instance, then for class, then for base class.

### Properties

- Properties basically make a method w/ a return appear as an attribute
- Can use python properties to force the use of getters and setters, but still using python getting and setting constructs
- Overrides something=object_x.prop to use the getter function, and object_x.prop=something to use the setter function

### Public/private/protected

No actual public/private/protected, is actually done just by programming convention. A preceding double underscore mangles the variable name so that the class name precedes it. A single underscore does nothing but suggests to other programmers that it's a protected variable. By convention, no underscore = public, single = protected, double = private.


Mocking/patching
----------------

- Patch passes in an instance of the patched object to your test method (or to every test method if you are patching at the class level). This is handy because it lets you set return values and side effects, or check the calls made

```python
@patch('some_module.sys.stdout')
def test_something_with_a_patch(self, mock_sys_stdout):
    mock_sys_stdout.return_value = 'My return value from stdout'

    my_function_under_test()

    self.assertTrue(mock_sys_stdout.called)
    self.assertEqual(output, mock_sys_stdout.return_value)
```

- If you just want to literally patch something out to ignore it then you can call patch with the following invocation

```python
@patch('some_module.sys.stdout', Mock())
def test_something_with_a_patch(self):
```

- That replaces sys.stdout in the some_module with a mock object and does not pass it to the method.
- To patch an attribute (itercampaigns):

```python
@patch.object(some.package.Class, 'someattr')
```

- If you want to patch y, but the import statement in the area you're testing contains 'from x import y' rather than 'import x.y', you must patch the module that contains 'from x import y' line, not simply x.y

```python
# Print traceback when attribute is accessed
import mock
import traceback
def hi():
    traceback.print_stack()
p = mock.PropertyMock(wraps=db.session, side_effect=hi)
type(db).session = p
# Also useful:
traceback.print_stack()
```


Decorators
----------

```python
# Decorator replaces original function signature
# Original function is passed in as an argument; replaced function is returned
def decorator(decorated_function):
    def replaced_function(input_to_replaced_func):
        print("pre")

        # This will call decorated function like normal
        print(input_to_replaced_func)
        decorated_function()

        print("post")
    return replaced_function

def orig_func():
    print("Hi")

@decorator
def orig_func_2():
    print("Hi")

# Identical:
decorator(orig_func)("Input to replaced function")
orig_func_2("Input to replaced function")

# Decorator with an argument:
def decorator2(decorator_arg):
    def real_decorator(decorated_function):
        def replaced_function(input_to_replaced_func):
            return decorator_arg + decorated_function(input_to_replaced_func) + decorator_arg
        return replaced_function
    return real_decorator

@decorator2("***")
def double_it(input_str):
    return input_str + input_str

print(double_it("goo"))
```


Logging
-------

Don't use .format or "%s"%var; the logger handles this internally. Instead, use logger.info("%s", var). Has better unicode support and performance.

See python's docs for its flowchart diagram for how it deals with loggers, formatters, handlers, etc.:  https://docs.python.org/2/_images/logging_flow.png


Argument passing
----------------

### Passing style

Python passes by reference. As long as you are working with the original variable reference (only doing mutations of it), it is still working on that same reference. The second you change the variable you are working with by doing something that's more than a simple mutation, a copy is made, and all changes are only local to that function.

```python
def test(input):
    input.append(1)
    print input
    input = ['hi']
    print input
    input.append('test')
    print input

inny = [1,2]
print inny
test(inny)
print inny
```

Output:

```python
 [1, 2]
 [1, 2, 1]
 ['hi']
 ['hi', 'test']
 [1, 2, 1]
```

### \*args and \*\*kwargs

- You would use \*args when you're not sure how many arguments might be passed to your function, i.e. it allows you pass an arbitrary number of arguments to your function.
- Similarly, \*\*kwargs allows you to handle named arguments that you have not defined in advance.
- You can use these along with named arguments too, but args and kwargs must be at the end.

```python
def func(required_arg, *args, **kwargs):
  print("req", required_arg)
  print("args", args)
  print("kwargs", kwargs)

func(1, "a", "b", x="x", y="y")
>>>('req', 1)
>>>('args', ('a', 'b'))
>>>('kwargs', {'y': 'y', 'x': 'x'})
```

Profiling
---------

```bash
# Profile externally
python -m cProfile -s time ./manage.py worker

# Profile function directly within ipython
%prun some_function()
```


Warnings
--------

Run python with `python -Wdefault` to show all warnings

Snippets
--------

### TCP and UDP client/server

#### TCP Server

```python
import socket

TCP_IP = ''
TCP_PORT = 5005
BUFFER_SIZE = 1024  # Normally 1024, but we want fast response

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind((TCP_IP, TCP_PORT))
s.listen(1)

conn, addr = s.accept()
print('Connection address:', addr)
while 1:
    data = conn.recv(BUFFER_SIZE)
    if not data:break
    print("received data:", data)
    conn.send(data)  # echo
conn.close()
```

#### TCP Client

```python
#!/usr/bin/env python

import socket


TCP_IP = '127.0.0.1'
TCP_PORT = 5005
BUFFER_SIZE = 1024
MESSAGE = "Hello, World!"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((TCP_IP, TCP_PORT))
s.send(MESSAGE)
data = s.recv(BUFFER_SIZE)
s.close()

print "received data:", data
```

#### UDP Server

```python
import socket

UDP_IP = ""
UDP_PORT = 5005

sock = socket.socket(socket.AF_INET, # Internet
                     socket.SOCK_DGRAM) # UDP
sock.bind((UDP_IP, UDP_PORT))

while True:
    data, addr = sock.recvfrom(1024) # buffer size is 1024 bytes
    print "received message:", data
```

#### UDP Client

```python
import socket

UDP_IP = "127.0.0.1"
UDP_PORT = 5005
MESSAGE = "Hello, World!"

print "UDP target IP:", UDP_IP
print "UDP target port:", UDP_PORT
print "message:", MESSAGE

sock = socket.socket(socket.AF_INET, # Internet
                     socket.SOCK_DGRAM) # UDP
sock.sendto(MESSAGE, (UDP_IP, UDP_PORT))
```

### Sqlite access

```python
import sqlite3
db = sqlite3.connect('database.db')
cursor=db.cursor()
cursor.execute('SELECT * from Power')
for row in cursor.fetchall():
    print(row[1], row[5])
```

virtualenv/pip
--------------

### Create virtualenv

```
virtualenv env
```

### Install local library

To copy project, in editable mode (to src dir)

```
pip install -e git+file:///home/shook/some-lib@HEAD#egg=some-lib
```

Can add [] for optional depndencies

```
pip install -e git+file:///home/shook/some-lib@HEAD#egg=some-lib[all]
```

To link to the directory (lib development mode); make sure src and site-packages are cleared out, and may need to remove/re-add interpreter in intellij
This creates an egg-link file and updates easy-install.pth; may need to synchronize intellij to pick up changes (ctrl+alt+y)

```
pip install -e /home/shook/some-lib
```

Library notes
-------------

### Coverage

```
coverage run manage.py test -n module
coverage report --omit="*/test*" --include=path/to/analyze/*,other/path/*
```


### Mypy

Adds type annotations and checking

```bash
pip install mypy-lang
```

Run with: python.exe C:\\Python34\\Scripts\\mypy ..\\performance_debugging_analysis\\src\\main.py

Add as an external tool to IntelliJ:

```
Program: C:/tools/python3/python.exe
Parameters: C:/tools/python3/scripts/mypy $FilePath$
```

### Pandas

Pandas is useful for any SIMD or linear algebra-like manipulations.
Basically, it can be very useful any time you need work on spreadsheet-like data.


```python
# Use pandas to parse xlsx/Excel workbooks
rows = read_excel("path_to_xlsx")
```

### Enforce

Use to enable runtime enforcement of python types.


### Flask

Using blueprints fixes circular import with using circular `app` import directly


### Sqlalchemy

By default, SQLAlchemy uses eager joins. This means the entire data structure and all subfields are grabbed during any access.

Lazy joins instead only bring in data when a particular data element is requested.


#### Life cycle

Can use automatic session scoping by tying into the app's lifecycle, but this means a session will last the full duration of the web request. I'm using a context manager to allow the flexibility of sharing code w/ non web apps.

```
# In flask:
def set_up_session_cleanup():
    def after_request(response):
        from website_workout.dao.utils import scoped_session_registry
        for scoped_session in scoped_session_registry.values():
            scoped_session.remove()
        return response
    app.after_request(after_request)
```


#### Sessions

- `scoped_session` is a thread-local registry of sessions
- calling `scoped_session(factory)()` gives you a session (one per thread)
- `id(scoped_session()()) == id(scoped_session()())`
- two calls to scoped_session will give the exact same session


#### Pools

pool_recycle refreshes connections older than n seconds upon access (not only idle ones)


#### Logging

Get info about sqlalchemy pools and connections (can use root sqlalchemy pool to get more info)

```
import sqlalchemy
import logging
logging.basicConfig()
logging.getLogger('sqlalchemy.pool').setLevel(logging.DEBUG)
```


#### Docs

- <http://docs.sqlalchemy.org/en/latest/orm/contextual.html#sqlalchemy.orm.scoping.scoped_session>
- <http://docs.sqlalchemy.org/en/latest/orm/session_basics.html#session-faq-whentocreate>
- <http://docs.sqlalchemy.org/en/latest/core/pooling.html>


### Numpy/scipy

#### Linear regression

```python
x = [[/1,_.1],_[20,_.2],_[3,_.3],_[4,_.4|1, .1], [20, .2], [3, .3], [4, .4]]
y = [1, 2, 3, 4]
coeffs = numpy.linalg.lstsq(x, y)[0]
print(coeffs)
```

Or

```python
def func(xval, a, b, c):
    return a * xval ** c + b

popt, pcov = scipy.optimize.curve_fit(func, x, y, maxfev=100000)
curvefit_x= func(x_val, *popt)
```

### Fabric and paramiko

For running ssh commands, paramiko is much better than fabric for anything requiring threading or dynamically determined hosts


#### Cross-correlation

```python
numpy.corrcoef([numpy.array(data1), numpy.array(data2)])
```

### pipdeptree

Shows dependencies between libraries (including any breakages)

```
pip install pipdeptree
pipdeptree
```


### piprot

Shows out of date libraries

```
pip install piprot
piprot -o requirements.txt
```


### jsonschema

Useful for validating JSON

```
pip install jsonschema
```

### Profile imports

```bash
PYTHONPATH=server python -X importtime -c 'import somecode; somecode.run()'
```
