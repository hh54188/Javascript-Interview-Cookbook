# Function

## Contents


## arguments

arguments像数组(array-like)，但不是数组

## Function expression & Function declaration

- 函数（所有变量？）声明，包括表达式形式的**声明**都不会有返回值

```
var fn = function () {}; // undefined
function fn() {} //undefined
var a = 1 //undefined
```

- 但赋值表达式会有返回值

```
var fn;
fn = function () {} // function () {}
var a;
a = 4 //4
```

## 函数生命周期发生的事情


###1. 当函数定义时：

>Function objects created with function declarations or function expressions have the scope chain of the execution context in which they are created assigned to their internal [[scope]] property.(http://www.jibbering.com/faq/notes/closures/#clIRExSc)

当函数定义时，无论你是用**函数表达式(function expression)**定义的函数，还是用**函数声明(function declaration)**定义的函数，这个函数都会获得当前执行上下文(execution context)中的scope chain，并且付给内置的**[[scope]]**属性

###1. 当函数执行时：

http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/

每一次函数被调用，都会创建一个执行上下文**execution context**。这个上下文被压入上下文栈(context stack)中，浏览器每次都会从栈的顶部开始执行，直到当前函数被执行完毕，该上下文弹出栈。 即使一个函数被执行多次，每一次都会生成新的上下文。最底层的当然是全局上下文(global execution context)，有且只有一个全局上下文.

简单看来执行上下文一共有三个属性

```
executionContextObj = {
    variableObject: { /* function arguments / parameters, inner variable and function declarations */ },
    scopeChain/scope: { /* variableObject + all parent execution context's variableObject */ },
    this: {}
}
```

http://www.jibbering.com/faq/notes/closures/#clIRExSc

>When an execution context is created a number of things happen in a defined order. **First, in the execution context of a function, an "Activation" object is created.** The activation object is another specification mechanism. It can be considered as an object because it ends up having accessible named properties, but it is not a normal object as it has no prototype (at least not a defined prototype) and it cannot be directly referenced by javascript code.

- **创建一个Activation Object对象（也是Variable Object），通过这个对象能访问函数中所有的变量，包括函数和变量还有传入的参数**

>The next step in the creation of the execution context for a function call **is the creation of an arguments object**, which is an array-like object with integer indexed members corresponding with the arguments passed to the function call, in order. It also has length and callee properties (which are not relevant to this discussion, see the spec for details). A property of the Activation object is created with the name "arguments" and a reference to the arguments object is assigned to that property.

- **把参数(arguments)引用添加入Activation Object中**

>**Next the execution context is assigned a scope**. A scope consists of a list (or chain) of objects. Each function object has an internal [[scope]] property (which we will go into more detail about shortly) that also consists of a list (or chain) of objects. The scope that is assigned to the execution context of a function call consists of the list referred to by the [[scope]] property of the corresponding function object **with the Activation object added at the front of the chain (or the top of the list).**

- **给上下文添加作用域，直接引用函数中的[[scope]]属性，在[[scope]]的最前端添加Activation object即成为scope chain**

>Then the process of "variable instantiation" takes place using an object that ECMA 262 refers to as the "Variable" object. However, **the Activation object is used as the Variable object (note this, it is important: they are the same object).** Named properties of the Variable object are created for each of the function's formal parameters, and if arguments to the function call correspond with those parameters the values of those arguments are assigned to the properties (otherwise the assigned value is undefined). **Inner function definitions are used to create function objects which are assigned to properties of the Variable object with names that correspond to the function name used in the function declaration. The last stage of variable instantiation is to create named properties of the Variable object that correspond with all the local variables declared within the function.**

- 进入变量初始化阶段，Activation Object变身为Variable Object
- 首先将**实参**赋值给Variable Object
- 再把根据**函数定义**创建的**函数对象**赋值给Variable Object
- 最后初始化变量(local variable)

所以**虽然函数和变量都会被前置，但函数比变量先前置**，所以会出现，即使函数定义在变量之后，也会被变量覆盖：
```
var a = 1;
function a() {};
typeof a // function
```


---

- arguments是对象而非数组(like-array) `typeof arguments;// Object`

- 在函数表达式`var f = function g() {}`中，函数名g只对内部可见

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