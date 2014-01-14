# Function

## Contents


- The execution context of a variable or function defines what other data it has access 
to, as well as how it should behave.

- Each execution context has an associated variable objectupon 
which all of its defined variables and functions exist. 

- When an execution 
context has executed all of its code, it is destroyed, taking with it all of the variables and functions 
defined within it

执行环境/上下文，会随着函数的执行完毕而销毁

- Each function call has its own execution context. Whenever code execution flows into a function, 
the function’s context is pushed onto a context stack. After the function has finished executing,
the stack is popped, returning control to the previously executing context.

- When code is executed in a context, a scope chainof variable objects is created. 

- The purpose of the 
scope chain is to provide ordered access to all variables and functions that an execution context has 
access to.

- Each context can search up the scope chain for variables and 
functions, but no context can search down the scope chain into another execution context.

- The front of the scope chain is always the variable object of the context whose code is 
executing.

- If the context is a function, then the activation objectis used as the variable object.

- The next variable object in the chain is from the containing context, and the next 
after that is from the next containing context. 

- Even though there are only two primary types of execution contexts, global and function (the 
third exists inside of a call to eval())

- When a function is called, an execution 
context is created, and its scope chain is created. 

- When compare()is defined, its scope chain is 
created,  作用域到底是什么时候创建的？

- When the function is called, an execution context is created and its scope chain is built up by 
copying the objects in the function’s [[Scope]]property

- A function that is defined inside another function adds the containing function’s activation object 
into its scope chain. 

------

- If, in your global code you call a function, the sequence flow of your program enters the function being called, creating a new execution context and pushing that context to the top of the execution stack

- So we now know that everytime a function is called, a new execution context is created.

- every call to an execution context has 2 stages
    - Creation Stage [when the function is called, but before it executes any code inside]:
        - Create variables, functions and arguments.
        - Create the Scope Chain.
        - Determine the value of "this".
    - Activation / Code Execution Stage:
        - Assign values, references to functions and interpret / execute code.

```
executionContextObj = {
    variableObject: { /* function arguments / parameters, inner variable and function declarations */ },
    scopeChain: { /* variableObject + all parent execution context's variableObject */ },
    this: {}
}
```



## Function Declaration

- function declaration(**attention: function declaration hoisting**):

```
sayHi();
function sayHi(){
    alert(“Hi!”);
}
```

- function expression

```
var sayHi = function () {
    alert("Hi");
}
```
##Scope Chain

A variable defined in a function is not visible 
outside the function, but a variable defined in a code block (an ifor a forloop) is 
visible outside the block.

##Lexical Scope 

In JavaScript, functions have lexical scope. This means that functions create their 
environment (scope) when they are defined, not when they are executed

```
function f1(){ var a = 1; f2(); }
function f2(){return a;}
f1(); //a is not defined
```

##Invoke


## CLOSURES

- **Closures are functions that have access to variables from another function’s scope**(Professional Javascript for Web Developers 3rd Edition)
- **闭包指在某处调用的函数能够访问另一处作用域里的变量**


- **a closure is the scope created when a function is declared that allows the
function  to  access  and  manipulate  variables  that  are  external  to  that  function.**(Secrets of the Javascript Ninja)
- Put another way, closures allow a function to access all the variables, as well as other functions, that are in scope when the function itself is declared
- **闭包是指一个函数被定义时形成的作用域，便于它能操作（访问）在这个函数以外的变量**

```
var later; 
function outerFunction() {
    var innerValue = 'samurai'; 
    function innerFunction() { 
        console.log(innerValue);
    } 
    later = innerFunction; 
}
outerFunction(); 
later();
```

**When we declared `innerFunction()` inside the outer function, not only was the function declaration defined, but a closure was also created that encompasses not only the
function declaration, but also all variables that are in scope at the point of the declaration.**

**When `innerFunction()` eventually executes, even if it’s executed after the scope in
which  it  was  declared  goes  away**

即使在`innerFunction`执行时它所在的**环境已经不存在了（请参考函数执行与栈的关系）**，但是它还能访问它的**作用域**，这个作用域就是闭包