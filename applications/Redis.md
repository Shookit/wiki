Redis
=====

Data type operations
--------------------

```
# get/set for strings
# everything is saved as strings, no numeric type
SET mykey "Hello"
SETEX myexpiringkey 10 "Expires after 10 seconds"

# LRANGE for arrays
# Negative numbers are allowed
LRANGE mylist 0 3
LRANGE mylist -2 3
```

DB monitoring commands
----------------------

```
INFO
MONITOR
```
