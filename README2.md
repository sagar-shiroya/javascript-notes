# How JavaScript works behind the scenes?

* [What is Execution Context?](#execution-context)
* [Type of Execution Context](#type-of-execution-context)
* [What is Call Stack and its different type?](#call-stack)
* [How Call Stack Works?](#how-call-stack-works)
* [What is Hoisting of Variables and Functions?](#hoisting)
* [What is Scope?](#scope)
* [What is Lexical Environment?](#lexical-environment)
* [What is Scope Chain?](#scope-chain)
* [What is Closure?](#closure)
* [Write a counter program using closure](#counter-program-using-closure)
* [What is a Block Scope?](#block-scope)
* [What is Temporal DeadZone?](#temporal-deadzone)
* [What is the differene between var and let?](#difference-between-var-and-let-keywords)
* [Best Practice: let/const or var?](#best-practice-letconst-or-var)


## Execution Context

Execution context consists of two phase:

1. Memory Creation
    - In the memory creation phase of an execution context, the JavaScript engine sets aside memory for the variables, functions, and objects that will be used within that context. It is in format of key:value pair for variable and function.
    - During this phase, JavaScript will declare any variables and functions that are defined within the current context and reserve memory for them, but not assign values to the variables yet. Set special placeholder named as “undefined” for it.
    - Function declarations are treated differently from variable declarations during the memory creation phase. For functions, the entire function definition is written in the memory.
2. Code Execution
    - During the code execution phase, the JavaScript engine will execute the code line by line, evaluating expressions, executing function calls, and assigning values to variables and replace undefined with actual values.

## Type of Execution Context

1. Global Execution Context
    - The global execution context is created when the JavaScript code is loaded into the browser and includes all global variables and functions.
2. Local Execution Context
    - Local execution contexts are created whenever a function is executed, and include all variables and functions declared within that function.

## Call Stack

- When the JavaScript code is first loaded into the browser, a global execution context is created and pushed onto the call stack.
- The global context contains all global variables and functions and is always present at the bottom of the call stack.
- Whenever a function is called, a new execution context is created for that function, and that context is pushed onto the top of the call stack. The engine then switches to the new context to execute the code within the function.
- When the function returns, its execution context is popped off the call stack, and the engine switches back to the previous context at the top of the stack.

## How call stack works?

```jsx
function func1() {
   func2();
}

function func2() {
   func3();
}

function func3() {
   // do something
}

func1();
```

When the code is executed, the following events occur:

1. The global execution context is created and pushed onto the call stack.
2. The **`func1`** function is called, and a new execution context for **`func1`** is created and pushed onto the call stack.
3. The **`func2`** function is called from within **`func1`**, and a new execution context for **`func2`** is created and pushed onto the call stack.
4. The **`func3`** function is called from within **`func2`**, and a new execution context for **`func3`** is created and pushed onto the call stack.
5. The code within **`func3`** is executed.
6. The execution context for **`func3`** is popped off the call stack.
7. The execution context for **`func2`** is popped off the call stack.
8. The execution context for **`func1`** is popped off the call stack.
9. The global execution context remains on the bottom of the call stack, and the code execution continues from where it left off.

## Hoisting

Hoisting in JavaScript refers to the behavior of the JavaScript engine in handling variable and function declarations within an execution context. 

Because of memory creation phase when code execution starts, even before that if you check global execution context memory has been assigned to that variable and function definition has been put in the global execution context. 

```jsx
var x;
console.log(x); // undefined, Not give an error because already there in memory
x = 10;
```

## Scope

- Scope in JavaScript refers to the visibility and accessibility of variables and functions within the code. In other words, it defines the region of the code where a variable or function can be accessed.
- There are two types of scope in JavaScript:
    - Global scope and
    - Local scope
- **Global scope** refers to the top-level context in which variables and functions are declared outside of any function. Variables and functions declared in the global scope are accessible from anywhere within the code.
- **Local scope** refers to the context within a function, where variables and functions are declared. Variables and functions declared in the local scope are only accessible within that function and are not visible outside of it.

```jsx
var x = 10; // global scope

function func1() {
   var y = 20; // local scope
   console.log(x); // 10
   console.log(y); // 20
}

func1();
console.log(y); // ReferenceError: y is not defined
```

## Variable Shadowing

In JavaScript, if a variable with the same name is declared both in the global scope and within a local scope (e.g. within a function), the local variable will take precedence over the global variable when the code is executed within that local scope.

```jsx
var x = 10; // global scope

function func1() {
   var x = 20; // local scope
   console.log(x); // 20
}

func1();
console.log(x); // 10
```

In this example, the global variable **`x`** is initially set to **`10`**. Within **`func1`**, a local variable **`x`** is declared and set to **`20`**. When the code is executed within **`func1`**, the local variable **`x`** takes precedence and is used. So when **`console.log(x)`** is called within **`func1`**, **`20`** is logged.

After **`func1`** is executed, the code returns to the global scope, and the global variable **`x`** retains its original value of **`10`**. So when **`console.log(x)`** is called outside of **`func1`**, **`10`** is logged.

This is known as variable shadowing, and it occurs when a local variable "shadows" the value of a global variable with the same name. The local variable temporarily masks the global variable within its scope, and the global variable is restored once the code returns to the global scope.

## Lexical Environment

- Every time a function is executed in JavaScript, a new lexical environment is created within the current execution context(GEC). This new lexical environment has access to variables and functions declared in the parent lexical environment, but it can also define its own variables and functions.

```jsx
function outerFunction() {
   var x = 10; // outerFunction's lexical environment

   function innerFunction() {
      var y = 20; // innerFunction's lexical environment
      console.log(x); // 10
      console.log(y); // 20
   }

   innerFunction();
}

outerFunction();
```

- In this example, **`outerFunction`** and **`innerFunction`** each have their own lexical environment. The **`outerFunction`**'s lexical environment has access to the **`x`** variable, which is declared within **`outerFunction`** and has a value of **`10`**.
- When **`innerFunction`** is executed, it creates its own lexical environment, which has access to the variables and functions declared within the parent **`outerFunction`** lexical environment. The **`innerFunction`** lexical environment also defines its own variable **`y`**, which has a value of **`20`**.

## Scope Chain

- Every time a function is executed in JavaScript, a new scope is created. This scope has access to the variables and functions declared in its parent scope, but it can also define its own variables and functions.
- The scope chain starts with the innermost scope and works its way up to the global scope, until it finds the variable or function it is looking for. If a variable or function is not found in the innermost scope, the JavaScript engine will look for it in the next outer scope, and so on, until it reaches the global scope. If the variable or function is not found in the global scope, a **`ReferenceError`** will be thrown.

```jsx
var x = 10; // global scope

function func1() {
   var y = 20; // func1's scope

   function func2() {
      var z = 30; // func2's scope
      console.log(x); // 10
      console.log(y); // 20
      console.log(z); // 30
   }

   func2();
}

func1();
```

## Closure

- A closure in JavaScript is like a secret room inside a bigger room. The secret room has everything it needs to do its own thing, like toys, books, and games. But it can also go outside and play with things from the bigger room, like toys and games.
- Just like the secret room can go outside and play with things from the bigger room, a closure can access variables and functions from its parent scope. And just like the secret room is safe and protected from the outside, the closure keeps its own variables and functions safe and protected from the outside.
- So, when you have a function inside another function, the inner function can remember and use variables and functions from the outer function, even after the outer function has finished running. This is what we call a closure.

```jsx
function outerFunction() {
  var x = 10;

  function innerFunction() {
    console.log(x);
  }

  return innerFunction;
}

var closure = outerFunction();
closure(); // logs 10
```

- When we call **`closure()`**, it logs **`10`** to the console, showing that the **`innerFunction`** still has access to the **`x`** variable even though the **`outerFunction`** has finished executing.


## Counter Program Using Closure

```jsx
function createCounter() {
  let count = 0;

  return function() {
    count += 1;
    return count;
  }
}

var counter1 = createCounter();
var counter2 = createCounter();

console.log(counter1()); // 1
console.log(counter1()); // 2

console.log(counter2()); // 1
console.log(counter2()); // 2

console.log(counter1()); // 3
console.log(counter1()); // 4
```

- In this example, the **`createCounter`** function returns a new function each time it's called. This new function has access to the **`count`** variable from the **`createCounter`** function, but the **`count`** variable is different for each instance of the returned function.
- When we call **`counter1()`**, it increments and returns the **`count`** variable for that instance of the function. When we call **`counter2()`**, it increments and returns a different **`count`** variable for that instance of the function.


## Block Scope

- Block scope in JavaScript refers to the scope of a variable that is defined within a block of code, such as within a loop or an if statement. In JavaScript, variables declared with the **`let`** or **`const`** keywords have block scope, meaning that their scope is limited to the block in which they are declared and any nested blocks.

```jsx
if (true) {
  let x = 10;
  console.log(x); // 10
}
console.log(x); // ReferenceError: x is not defined
```

- In this code, the variable **`x`** is declared within the block of the if statement, and its scope is limited to that block. When the **`console.log`** statement outside of the if block attempts to access **`x`**, a **`ReferenceError`** is thrown, because **`x`** is not accessible in the outer scope.
- In contrast, variables declared with the **`var`** keyword in JavaScript have function scope, meaning that their scope is limited to the function in which they are declared, but they are accessible throughout the function, including within any nested blocks.

```jsx
if (true) {
  var x = 10;
  console.log(x); // 10
}
console.log(x); // 10
```

## Temporal DeadZone

- The temporal dead zone (TDZ) in JavaScript is a period of time in which a variable declared with the **`let`** or **`const`** keywords is in a "limbo" state, and is not accessible or assignable until the declaration has been fully processed.

```jsx
console.log(x);  // ReferenceError: x is not defined
let x = 10;
```

- In this code, the variable **`x`** is declared with **`let`**. The **`console.log`** statement attempting to access **`x`** is executed before the declaration of **`x`** has been processed, and thus it triggers a **`ReferenceError`**. This period of time between the start of the code and the declaration of **`x`** is called the **temporal dead zone**.
- The TDZ helps to ensure that variables declared with **`let`** or **`const`** are only accessible in the code after they have been properly declared and initialized, and not before.

## Difference between `var` and `let` keywords

1. Scope
    - Variables declared with **`var`** have function scope, meaning that their scope is limited to the function in which they are declared.
    - Variables declared with **`let`** have block scope, meaning that their scope is limited to the block in which they are declared and any nested blocks.
2. Hoisting
    - Variables declared with **`var`** are hoisted, meaning that they are moved to the top of their scope and are accessible throughout the scope, including before their declaration.
    - Variables declared with `let` are also hoisted, but they are in temporal deadzone. So can’t access them before declared/initialised it.
3. Redeclaration
    - Variables declared with **`var`** can be redeclared within the same scope, even if they have already been declared.
    - Variables declared with **`let`** cannot be redeclared within the same scope.
4. Temporal DeadZone
    - Variables declared with **`let`** or **`const`** are subject to the temporal dead zone (TDZ), a period of time in which they are in a "limbo" state and are not accessible or assignable until their declaration has been fully processed.
    - Variables declared with **`var`** are not subject to the TDZ.

## Best Practice: let/const or var?

- The current best practice in JavaScript is to use **`let`** instead of **`var`** when declaring variables.
- **`let`** has block scope, meaning that its scope is limited to the block in which it is declared and any nested blocks. This provides a more predictable and flexible way of managing variables and their scopes, compared to **`var`**, which has function scope and can lead to unintended behavior in certain cases.
- **`let`** also has other advantages over **`var`**, such as not being hoisted, not allowing redeclaration within the same scope, and being subject to the temporal dead zone, which can help prevent unintended behavior and improve code readability.
- Using **`const`** is also a good practice when declaring variables that will not change, as it provides an added layer of protection against accidental modification.
- So in summary, using **`let`** and **`const`** is currently considered the best practice in JavaScript, while using **`var`** is discouraged.
