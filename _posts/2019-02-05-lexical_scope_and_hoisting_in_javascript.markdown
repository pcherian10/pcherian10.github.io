---
layout: post
title:      "Lexical Scope and Hoisting in JavaScript"
date:       2019-02-05 20:57:21 +0000
permalink:  lexical_scope_and_hoisting_in_javascript
---



These were easily a couple of the morenuanced concepts I had come across since I had started my coding journey. Spending the time to understand them was well worth as it helped reduce the potential of rather easy-to-make errors to show up in code. So let’s dive in quickly and elucidate each concept.

## Scope
I think it’s important to understand scope before diving into hoisting. Scope refers to the notion of the availability of a variable across a block, defined by `{}`or generally across your code. Not all variables are available — i.e, maintain their values outside a block or in specific areas. There are three main components that lead to an strong understanding scope broadly — global scope, block scope and lexical scope.

**Global scope** refers to availability of variable to be used throughout the code, regardless of function. Declaring variables in the global scope can advantageous at times, but it’s important to keep track as to prevent overuse that may lead to bugs. There are a couple of ways to initialize variables with a global scope as shown below.

1. The variable can be initialized first and outside of any explicit or anonymous functions with or without `var`.

```var x=1
function scope() {
  return x;
}```

Calling `scope()` here will result in ‘1’ being returned

2.* Undeclared* variables that are locally created are global by default.

```function scope () {
  x = 1
  return x;
}
function testingScope () {
  var newX = scope() + 1;
  return newX;
}```

In the above code, what is the value of calling `testingScope() `? Since x was undeclared in the `scope()` function, it’s scope is global (even though it was defined in a function). Note that variables declared or undeclared (with var) in the window context will have global scope.

*Block scope* is restricted to, unsurprisingly, to the block {} it was defined in. This is true of all variables declared withvar. Thus, as a very simple example, the below is true.

```function outerFunction () {
  
  function innerFunction () {
    var x = 1
    return x;
  }
  
  return x;
}```

`x` is still undefined, despite the fact that the `innerFunction()` is inside `outerFunction()` and had declared and defined x Thus we say that the scope of the variable x is just the `innerFunction()`

The last important scoping concept in JavaScript and it’s best demonstrated with an outer / inner function example like the above. Lexical scoping refers to the JS interpreter’s method of determining scope. Going back to the example above, what if we defined a variable `y` in the `outerFunction() `?

```function outerFunction () {
  var y = 2;
  
  function innerFunction () {
    var x = 1 
    return x + y;
  }
  
  return innerFunction();
}```

In this case, `y` will be available to `innerFunction()` because it’ s it’s defined within `outerFunction()`. Lexical scoping refers to the interpreter’s convention of defining the scope from the inward out. The innermost function’s variables will only be available to the inner functions, whereas the functions defined on the outside will be available to use in the inner functions as seen in the example above.

This covers the basics of scope in JavaScript. Now, we’ll move on to a more nuanced concept — hoisting, which is a more specific quirk of JS that can easily cause bugs in code if not accounted for.

## Hoisting — Key Takeaways
Basically, the problems associated with hoisting are resolved with two actions: declaration and initialization of variables. Specifically, declaring and initializing variables at the top of the current scope or block will all but eliminate scope-related or variable related errors that may occur later in the code.

**The Why**

Hoisting is the result of the JS interpreter compiling code in a certain way. The interpreter’s default behavior is to move declarations to the top of the scope or ‘hoist’ when compiling code. Thus, if a variable’s used before declaration and initialization, the code will compile, but the variable will be undefined. Here’s an example to illustrate.

`function hoist() {
  x += 5
  var x;
  x = 5
  return x;
}`

What’s the value of `x` when the above function is called? It’s actually just 5. The interpreter hoists the value of `x` , but the variable has neither been declared or initialized at this point, and thus the variable is undefined even when we’ve declared and initialized the variable later in the code. To get the value of 10 as intended, we would make the following changes:

`function hoist() {
  var x;
  x = 5;
  x += 5;
  return x;
}`

This ensures that the variable has been declared and initialized in order and the code successfully returns the intended result.

## Conclusion

Both concepts easily would’ve escaped my radar had they not been bought to my attention explicitly. They’re easy to miss, but being aware of them helps you structure your code to ensure that variable-definition related bugs don’t materialize. More importantly, it allows for more efficient debugging if they do pop up.

Thanks for reading and happy coding!





