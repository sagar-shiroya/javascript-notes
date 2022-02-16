# Javascript Notes
* [What is Closure in JavaScript?](#what-is-closure-in-javascript)
* [setTimeout Function in JavaScript](#settimeout-function-in-javascript)
* [Different Types of Functions and Terminologies](#different-types-of-functions-and-terminologies)
* [Callback Functions in JavaScript](#callback-functions-in-javascript)

__All answers are inspired by Akshay Saini's Namaste JavaScript Series__

## What is Closure in JavaScript?

* Function bundled along with it's lexical scope is Closure.
* Lexical Scope Environment: If function needs to access specific variable then it first goes to its local scope. When it doesn't find locally, it goes to its lexical parent. So it's around the function `y()`, if you consider the example below. `var a = 7;` is in lexical parent of function y.

```javascript
function x() {
    var a = 7;
    function y() {
        console.log(a);
    }
    return y;
}
var z = x();
console.log(z);  // value of z is entire code of function y.
```

***A closure is a function** that has access to its outer function scope even after the function has returned. Meaning, A closure can remember and access variables and arguments reference of its outer function even after the function has returned.*

### Advantages of Closure
* Module Design Pattern
* Currying
* Functions like `once`
* memoize
* setTimeouts
* Iterators etc.

### Disadvantages of Closure
* Overconsumption of Memory

## setTimeout Function in JavaScript

> Time, Tide & JavaScript wait for none.
By [Akshay Saini](https://github.com/akshaymarch7)

* Below is the example of setTimeout function:
```javascript
function x() {
  var i = 1;
  setTimeout(function() {
      console.log(i);
  }, 3000);
  console.log("Hello");
}
x();
```
**Output**
```
Hello
1 (after 3 seconds)
```
* Javascript assign the memory for the function x somewhere and set the timer of 3 seconds for it & JS Engine continues to execute next lines. So it prints `Hello` first and then after 3 seconds, it prints 1.

### Write a JS Code to print 1,2,3,4,5 using setTimeout with wait of 1,2,3,4,5 seconds respectively
We write below code as obvious
```javascript
function x(){
  for(var i = 1; i<=5 ; i++){
    setTimeout(
      function(){console.log(i)}, 
      i * 1000
    );
  }
}
x();
```
**Output**
```
6
6
6
6
6
```
This is happeneing because JS engine will not wait while timer is running. So before timer gets completed, loop complete and has latest value as 6 in its memory location. So for each i, it points to same memory location having value as 6. 
This issue can be resolved by using either **closures** or **let**.

#### Solution using let:
```javascript
function x(){
  for(let i = 1; i<=5 ; i++){
    setTimeout(
      function(){console.log(i)}, 
      i * 1000
    );
  }
}
x();
```
As `let` variable has __block scope__, so it will take seperate memory location for each time i iterates and has different value in it.

#### Solution using closure:
```javascript
function x(){
  for(var i = 1; i<=5 ; i++){
    function timer(a){
      setTimeout(
        function(){console.log(a)}, 
        a * 1000
      );
    }
    timer(i);
  }
}
x();
```
Each time loops runs, it calls the function timer() and pass different value. So each function iteration has seperate value and memory pointer pointing to it.

## Different Types of Functions and Terminologies
 
### Function Statement or Function Declaration
Below way of writing function is called function statement:
```javascript
function x() {
    console.log('test');
}
x(); //test
```
### Function Expression
Below way of writing a function  is called function expression:
```javascript
var a = function() {
    console.log('test2');
}
a(); //test2
```
### Difference between Function Statement & Expression
Key difference between function statement and expression is the hoisting only. We can't call function before definining in  case of expression, while we can call function before defining in function statement.
```javascript
a(); //function a
b(); //Uncaught TypeError: b is not a function 
function a() {console.log('function a');} //Function Statement
var b = function() {console.log('function b');} //Function Expression
```
Note: In case of function expression, b is only placeholder in the memory location and value is undefined. So once execution context comes to the line where it defined then only b's value set as function. Before that we can't call the function same as we can call in function statement.
### Anonymous Functions
Anonymous function is a function without the name. We can't use it in below way.
```js
function() { 
    //...
}
```
* This results into an error as `Uncaught SyntaxError: Function statements require a function name`.
* We can use anonymous function as value.
* We can assign it to variable(as we did in function expression), pass it to a function arguments etc.
**Examples:**
```js
//Example 1
var anonym = function() { //... }

//Example 2
function a(func){
    console.log(func);
}
a(function(){console.log('this is function b');}); //prints "ƒ (){console.log('this is function b');}"
```
### First Class Functions or First Class Citizens
We can pass function as arguments into another function and also we can return the function as a value from function. This ability of passing and returning a function is called First Class Function. This is also available in other programming languages.
```js
// passing the function as arguments
var b = function(param1) {
  console.log(param1); // prints " f() {} "
}
b(function(){});

//returning the function as value
var b = function(param1) {
  return function() {
  }  
}
console.log(b()); // prints "ƒ () { }"
```
This is also called first class citizens. 
### Named Function Expression 
Named function expression is same as function expression, but this function has a name instead of being anonymous.
```js
var a = function xyz() {
    console.log('test2');
}
a(); //test2
```
__Note: We can't call function by `xyz()`__
### Parameters v. Arguments
**Parameters:** It's labels or identifiers used when defining function. <br>
**Arguments:** When we call function and we pass the value into function. Those values called arguments.
```js
function a(param1, param2){
    console.log(param1, param2);
}
a(7,8);
```
In above example, param1 & param2 is parameters. While 7 & 8 is referred as arguments.

## Callback Functions in JavaScript
* **Callback Function** is a function passed as argument to another function. It's called callback function because it will get called sometime later during execution.
* For e.g. setTimeout() has first argument as callback function and that callback function will get called once timer got expired which passed as second argument.
* Once javascript execution completed, callstack gets empty. But if there's still setTimout()'s timer is running then once timer gets expired then callback function will be added to callstack after that timeout time period.
```js
setTimeout(function(){
    console.log('after 3 seconds');
}, 3000);
```

### Javascript is a synchronous and single-threaded language
* Synchronous: Javascript executes all the statements one by one.
* Single threaded language: It means that JavaScript has only single callstack which is used to execute the program. Everything in javascript executed, will be executed from this one call stack only.

### Blocking the main thread
* If there is any function in your code which does very heavy operation (and e.g. if it takes 30 seconds to complete the execution of whole function). During this time, JS won't be able to execute any other of code as there's only one call stack. This event is called blocking the main thread. 
* As there's only one call stack, so we should never block our main thread. We should always try to use async operations for things which takes time.(e.g. setTimeout)

## Event Listeners in JavaScript
* We will create a button in html and attach event to it.
```js
// index.html
<button id="clickMe">Click Me!</button>

// index.js
document.getElementById("clickMe").addEventListener("click", function xyz(){ //when event click occurs, this callback function (xyz) is called into callstack
      console.log("Button clicked");
});
```
* Let's implement counter which will take care of how many times the button has clicked. Using global variable (This is not good practice as anyone can change `count` variable)
```js
let count = 0;
document.getElementById("clickMe").addEventListener("click", function xyz(){ 
    console.log("Button clicked", ++count);
});
```
* Use closures for Data Abstraction
```js
function attachEventListener() {
    let count = 0;
    document.getElementById("clickMe").addEventListener("click", function xyz(){ 
        console.log("Button clicked", ++count);
    });
}
attachEventListener();
```
* In Second example, once execution of javascript gets finished. Event Listener has been added in your browser with the closures. It has lexical scope and count variable as well. Once someone click on the button, that count value gets increased by one.

### Garbage Collection and Remove Event Listeners
* Event listeners are heavy as they form the closures and store everything around it in the browser. Because event can be triggered anytime even after the JS execution gets completed for given script.
* So even when call stack is empty, EventListener won't free up memory allocated to count as it doesn't know when it may need count again.
* We should remove event listeners when we don't need them (garbage collected). onClick, onHover, onScroll all in a page can slow it down heavily.

