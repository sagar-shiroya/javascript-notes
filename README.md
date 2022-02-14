# Javascript Notes
* [What is Closure in JavaScript?](#what-is-closure-in-javascript)
* [setTimeout Function in JavaScript](#settimeout-function-in-javascript)

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
  for(let i = 1; i<=5 ; i++){
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
