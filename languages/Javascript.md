Javascript
==========

Callback hell
-------------

Don't overuse anonymous functions. This leads to confusing over-nesting. Flatten as much as possible. Prefer named functions that are generalizable.


Common ES6 procedures
---------------------

```
// Get element by ID
const domElement = document.getElementById("wodata")

// Get element children
const elementChildren = domElement.children;

// Iterate over children
someArray = []
Array.from(elementChildren).forEach((child, index) => {  // index is optional
    // Get data from data attributes
    const date = parseDate(child.dataset.date);

    // Look at text content
    if (child.textContent === "searcher") {}

    //Append to array
    someArray.push({date});

// Single-line anonymous function
() => (somereturn)

// Multi-line anonymous function
() => {
    somestatement;
    return somereturn;
}

});
```


Prototypes
----------

In Javascript, EVERY SINGLE object has a prototype object inside of it (e.g., object1.prototype). If you add to this prototype, all objects of that same constructor will get that additional functionality. That said, ES6 has class support, so you can probably just use that instead.

Example 1:

```
function Pet(name, species, hello){
    this.name = name;
    this.species = species;
    this.hello = hello;
}

Pet.prototype.sayHello = function(){
    alert(this.hello);
}

var rufus = new Pet("Rufus", "cat", "miaow");
rufus.sayHello();
```

Example 2:

```
String.prototype.sheep = function(){
    return "baaah " + this;
}

var hello = "hello world";
alert(hello.sheep());
```

File loading via Javascript
---------------------------

```
$(function(){
    $("#includedContent").load("b.html"); 
});
```

When accessing another file via javascript (like above) on the local filesystem, you will get an error. Use python to launch an http server to allow this properly.

```bash
python -m http.server
```

Module formats
--------------

Two major JS module specs: AMD and CommonJS.

```
    CommonJS compliant:
        // package/lib is a dependency we require
        var lib = require("package/lib");

        // behavior for our module
        function foo(){
            lib.log( "hello world!" );
        }

        // export foo to other modules as foobar
        // base foo is not exported as-is
        exports.foobar = foo;

    AMD compliant:
        // package/lib is a dependency we require
        define(["package/lib"], function (lib) {
            // behavior for our module
            function foo() {
                lib.log("hello world!");
            }

            // export foo to other modules as foobar
            // base foo is not exported as-is
            return {
                foobar: foo
            }
        });

    CommonJS format w/ RequireJS wrapper:
        define(function(require, exports, module) {
            // package/lib is a dependency we require
            lib = require 'package/lib'

            // behavior for our module
            function foo() {
                lib.log("hello world!");
            }

            // export foo to other modules as foobar
            // base foo is not exported as-is
            return {
                foobar: foo
            }
        }

    Somewhere else the module can be used with:
        require(["package/myModule"], function(myModule) {
            myModule.foobar();
        });
```


Q Promises
----------

```
//
// Imports
//
var q = require('q');

//
// Main
//
// Promise terminology
// fulfilled - The action relating to the promise succeeded
// resolve   - The action relating to the promise succeeded
// rejected  - The action relating to the promise failed
// pending   - Hasn't fulfilled or rejected yet
// settled   - Has fulfilled or rejected
// reason    - Error variable

var liftedPromise = q.resolve("x");

// Build a promise directly w/ callbacks for if it works or doesn't
var promise = new q.Promise(function(resolve, reject) {
  var switchVar = true;

  if (switchVar) {
    resolve("Stuff worked!");
  }
  else {
    reject(Error("It broke"));
  }
});

// Resolve the promise and call either the success
var resolve = promise.then(
    // Successful block
    function (input) {
        console.log(input);
    }
,
    // Failed block
    function (reason) {
        console.log(reason);
    }
);

// Resolve the promise and return something; can then use that return in a second then() callback
var resolveChain = promise.then(
    // Successful block
    function (input) {
        return "wasSuccess";
    }
,
    // Failed block
    function (reason) {
        return "wasError";
    }
).then(
    // Uses return from previous 'then'; is not an error, so always uses fulfilled block
    function (secondInputSuccess) {
        console.log(secondInputSuccess);
    }
);

// Promise.all takes an array of promises and creates a promise that fulfills when all of them successfully complete. You get an array of results (whatever the promises fulfilled to) in the same order as the promises you passed in.

// Successful promise
var successful = new q.Promise(function(resolve, reject) {
    resolve("Ok");
});

// Unsuccessful promise
var unsuccessful = new q.Promise(function(resolve, reject) {
    reject(Error("It broke"));
});

q.all([successful, unsuccessful]).then(
    function (input) {
        console.log("All worked");
    }
,
    function (reason) {
        console.log("Not all worked");
    }
);


q.all([successful, successful]).then(
    function (input) {
        console.log("All worked");
    }
,
    function (reason) {
        console.log("Not all worked");
    }
);


// When a promise is either resolved or rejected, it is 'dead'; any further resolve/reject calls are ignored
var q1 = q.defer();
q1.promise.then(
        function(){
            console.log('q1 success')
        }
        ,
        function(){
            console.log('q1 reject')
        }
);

q.delay(100).then(function(){
    q1.resolve()
});


q.delay(10).then(function(){
    q1.resolve()
    q1.reject()
});
```

