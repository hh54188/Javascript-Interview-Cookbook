# Function

## FAQ 

- `(function () {})()` VS `function fn() {}` VS `(function () {})`
- 请解释什么是闭包以及闭包的原理
- 请解释`this`关键字有哪些含义
- 函数有哪些用法？

## `(function () {})()` VS `function fn() {}()` VS `(function () {})`

http://kangax.github.io/nfe/

后者有什么问题？

创建一个函数对象(functoin object)的方式：函数表达式(function expression)和函数声明(function declaration)

```
FunctionDeclaration :
function Identifier ( FormalParameterList opt ){ FunctionBody }

FunctionExpression :
function Identifier opt ( FormalParameterList opt ){ FunctionBody }
```

函数表达式的函数名是可有可无的，但如果出现了，浏览器怎么区分是函数表达式还是函数声明？

**通过上下文**

- 如果`function foo() {}`是赋值表达式的一部分(assignment expression)，那么它一定是函数表达式

- 如果`function foo() {}`在双括号中`(function foo() {})`那么一定是表达式，因为`(`和`)`是只能包含表达式的操作符

- 如果`function foo() {}`是在一个函数体内或者暴露在程序中，那么一定是函数声明

```
function foo(){}    // declaration, since it's part of a <em>Program</em>
var bar = function foo(){};     // expression, since it's part of an <em>AssignmentExpression</em>

new function bar(){};   // expression, since it's part of a <em>NewExpression</em>
// 注意new关键字为运算符，详情可参考“Javascript权威指南”

(function(){
  function bar(){}  // declaration, since it's part of a <em>FunctionBody</em>
})();

```

- 使用`eval`来转化JSON时，也必须加上括号，避免花括号`{`被解释成`块`

```
eval('{"lee": "hello"}') // error
eval('({"lee": "hello"})') // correct
```

## `(function () {})` VS `(function () {})()`

http://stackoverflow.com/questions/8228281/what-is-this-iife-construct-in-javascript

前者返回（创建）了一个函数表达式，后者立即执行了这个函数表达式，并且后者为IIFE(Immediately-Invoked Function Expressions)

这么做通常是为了避免污染全局变量




## arguments

- arguments像数组(array-like)，但不是数组,`typeof arguments // object`

- arguments也可以被重新赋值

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

而当它被执行时，首先会创建上下文，而上下文中的scope chain对象，就是内置的`[[scope]]`对象加上当前的`Variable Object`

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

- **创建一个名为arguments的arguments object，把引用添加入Activation Object中**

>**Next the execution context is assigned a scope**. A scope consists of a list (or chain) of objects. Each function object has an internal [[scope]] property (which we will go into more detail about shortly) that also consists of a list (or chain) of objects. The scope that is assigned to the execution context of a function call consists of the list referred to by the [[scope]] property of the corresponding function object **with the Activation object added at the front of the chain (or the top of the list).**

- **给上下文添加作用域，直接引用函数中的[[scope]]属性，在[[scope]]的最前端添加Activation object即成为scope chain**

>Then the process of "variable instantiation" takes place using an object that ECMA 262 refers to as the "Variable" object. However, **the Activation object is used as the Variable object (note this, it is important: they are the same object).** Named properties of the Variable object are created for each of the function's formal parameters, and if arguments to the function call correspond with those parameters the values of those arguments are assigned to the properties (otherwise the assigned value is undefined). **Inner function definitions are used to create function objects which are assigned to properties of the Variable object with names that correspond to the function name used in the function declaration. The last stage of variable instantiation is to create named properties of the Variable object that correspond with all the local variables declared within the function.**

- 进入变量初始化阶段，Activation Object变身为Variable Object
- 根据传入参数的实际情况，依次添加进VO中，如果arguments object中存在该属性，把值赋值给该属性，**实际参数始终是与arguments object分开来存储的**

```
fooExecutionContext = {
    variableObject: {
        arguments: {
            0: 22,
            length: 1
        },
        i: 22
    },
    scopeChain: { ... },
    this: { ... }
}

```
```
// 对arguments重新赋值也不会影响实际参数
function foo(i) {
    arguments = [2];
    console.log(i) // 22
    console.log(arguments) // [2]
}

foo(22);
```
- 再把根据**函数定义**创建的**函数对象**赋值给Variable Object
- 最后初始化变量(local variable)

所以**虽然函数和变量都会被前置，但函数比变量先前置**，所以会出现，即使函数定义在变量之后，也会被变量覆盖：
```
var a = 1;
function a() {};
typeof a // function
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