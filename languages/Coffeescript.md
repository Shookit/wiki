Coffeescript
============

Basic Syntax
------------


```
# JS object syntax
{} # is just a standard JS object; effectively a map/dict

title=1
body=2
selection=3
{title, body, selection} is shorthand for {title: 1, body: 2, selection: 3)

# Classes
'@' means 'this'

# Flow control
if true == true
  "We're ok"

if true != true then "Panic"

# Loops
for name in ["Roger", "Roderick", "Brian"]
  alert("Release #{name}")

# Arrays
range = [1..5]

# Classes
class Animal
  constructor: (@name) ->
  alive: -> false
```


Functions
---------

```
# Calling
function param, param
function(param, param)

# Don't nest bracketless function calls (in any lang that allows them)
Don't do: times plus 1,2 , minus 5,2
Instead: times plus(1,2), minus(5,2)
Really though, just use parens: times(plus(1,2), minus(5,2))

# Lambdas
times = (a, b) -> a * b
times = (a = 1, b = 2) -> a * b


# It's allowable to omit arguments when calling a function; omitted args will be null.
test=(x, y) -> return (x, y)
test(1, 2) = 1, 2
test(1) = 1, null
```


Fat arrows
----------

'this' can change depending on the calling context. To make 'this' static, use fat arrows for callbacks in CS.
Fat arrow notation (`=>`)is used to lock to the calling context


Closures
--------

Closures don't work as-is; need to use "closure wrappers" (related to 'do' in coffeescript).
Say a callback is called asynchronously in a loop, and uses the loop variable as a closure.
If closure wrappers aren't used, will use the final loop var each time instead of the current loop iteration.


