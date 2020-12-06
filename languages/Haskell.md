Haskell
=======

Types
-----

### What type is this

```
:t 'a'
>> 'a' :: Char

# :: means  "is of type". Types are always capitals; if a lowercase is returned, is a generic type

:t head
>> head :: [a] -> a
```

### Declare type of a function

```
# "b is of type Char"
b :: Char
```


### Declare a complex data type

```
data Person = Person { firstName :: String
  , lastName :: String
  , age :: Int
  , height :: Float
  , phoneNumber :: String
  , flavor :: String
} deriving (Show)
```

Functions
---------

### Defining a function

```
# When calling a function, first is the function's name, 1 to n-1 are parameters, final is the return
# Functions are very similar to constructors. Something is always returned (no concept of 'void' functions

addThree :: Int -> Int -> Int -> Int
addThree x y z = x + y + z
```


### Partial functions

If you have a function like `a->a->a` and only call the first, you can then use that function elsewhere if you want since the other a isn't filled in.

