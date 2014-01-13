# Function

## Contents

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