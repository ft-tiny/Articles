# Closures

![Closures image](/images/Closure-JS.png)

A **closure** is the combination of a function bundled together (enclosed) with references to its surrounding state (the **lexical environment**). In other words, a closure gives you access to an outer function's scope from an inner function. In JavaScript, closures are created every time a function is created, at function creation time.

<br>

## Lexical scoping

Lexical scoping defines the by the position of that variable declared in the source code.
For example:

```
function outer() {
  var name = 'test'; // name is a local variable created by init
  function inner() { // inner() is the inner function, a closure
    console.log(name); // use variable declared in the parent function
  }
  inner();
}
outer();
```

`outer()` creates a local variable called `name` and a function called `inner()`. The `inner()` function is an inner function that is defined inside `outer()` and is available only within the body of the `outer()` function. Note that the `inner()` function has no local variables of its own. However, since inner functions have access to the variables of outer functions, `inner()` can access the variable `name` declared in the parent function, `outer()`.

This is an example of _lexical_ _scoping_, which describes how a parser resolves variable names when functions are nested. The word _lexical_ refers to the fact that lexical scoping uses the location where a variable is declared within the source code to determine where that variable is available. Nested functions have access to variables declared in their outer scope.

<br>

## Static scope vs dynamic scope

- **Static scope** refers to a piece of code whose scope has been determined before it is executed.
- **Dynamic Scope** – The scope of a function is determined when a function is called.
  JavaScript uses lexical scope, that is static scope.

```
// Static scopes:
var a = 2;
function foo() {
  console.log(a); // 2
}
function bar() {
  var a = 3;
  foo();
}
bar();
```

```
// Dynamic scopes:
var a = 2;
function foo() {
  console.log(a); // 3
}
function bar() {
  var a = 3;
  foo();
}
bar();
```

<br>

## Lexical Environment

Every execution context has a reference to its outer environment, and that outer environment is called Lexical Environment.
A lexical environment consists of two main components: the **environment record** and a **reference** to the outer (parent) lexical environment:

1.  The **environment record** is the actual place where the local variables, arguments and function declarations are stored.
2.  The **reference** to the outer environment means it has access to its outer (parent) lexical environment.

```
lexicalEnvironment = {
  environmentRecord: {
    <identifier> : <value>,
    <identifier> : <value>
  }
  outer: <Reference to the parent lexical environment>
}
```

So let’s again take a look at above code snippet:

```
var a = 'Hello World!';
function foo() {
  var b = 25;
  console.log('Inside foo function');
}
foo();
console.log('Inside global execution context');
```

When the JavaScript engine creates a global execution context to execute global code, it also creates a new lexical environment to store the variables and functions defined in the global scope. So the lexical environment for the global scope will look like this:

```
globalLexicalEnvironment = {
  environmentRecord: {
    a : 'Hello World!',
    foo : <reference to function object>
  }
  outer: null
}
```

Here the outer lexical environment is set to `null` because there is no outer lexical environment for the global scope.

When the engine creates execution context for `foo()` function, it also creates a lexical environment to store variables defined in that function during execution of the function. So the lexical environment of the function will look like this:

```
functionLexicalEnvironment = {
  environmentRecord: {
    b : 25,
  }
  outer: <globalLexicalEnvironment>
}
```

The outer lexical environment of the function is set to the global lexical environment because the function is surrounded by the global scope in the source code.

<br>

## Garbage collection

Usually, a Lexical Environment is removed from memory with all the variables after the function call finishes. That’s because there are no references to it. As any JavaScript object, it’s only kept in memory while it’s reachable.
In that case the Lexical Environment is still reachable even after the completion of the function, so it stays alive.
For example:

```
function foo() {
  var value = 123;
  return function() {
    console.log(value);
  }
}
var bar = foo();
```

If `foo()` is called many times, and resulting functions are saved, then all corresponding Lexical Environment objects will also be retained in memory.

A Lexical Environment object dies when it becomes unreachable (just like any other object). In other words, it exists only while there’s at least one nested function referencing it.

In the code below, after the nested function is removed, its enclosing Lexical Environment (and hence the `value`) is cleaned from memory:

```
function  foo() {
  var value = 123;
  return  function () {
	console.log(value);
  };
}
var bar = foo(); // while bar function exists, the value stays in memory
bar = null; // ...and now the memory is cleaned up
```

<br>

## JavaScript Closure examples

JavaScript closures in a loop
```
for (var i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i);
  });
}
// Output: 3
// Output: 3
// Output: 3
```

Using the IIFE solution or passing parameter to a callback function
```
for (var i = 0; i < 3; i++) {
  (function (i) {
  setTimeout(function () {
    console.log(i);
  });
  })(i)
}
// or
for (var i = 0; i < 3; i++) {
  setTimeout(function (i) {
    console.log(i);
  }, 0, i);
}
// Output: 0
// Output: 1
// Output: 2
```

Using let keyword in ES6
```
for (let i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i);
  });
}
// Output: 0
// Output: 1
// Output: 2
```

Private methods with closures
```
var counter = (function() {
  var privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }

  return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  };
})();

console.log(counter.value());  // 0.

counter.increment();
counter.increment();
console.log(counter.value());  // 2.

counter.decrement();
console.log(counter.value());  // 1.
```
