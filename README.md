# Javascript 大复习！

这个项目是某次面试后，**痛定思痛**，用于把模糊或者压根不懂的知识点总结下来

下节是题目，下下节是答案

## 问题

### Javascript

- 继承的原理是怎样的？请说出至少三种继承的方法
- 什么是闭包？
- `this` 关键词有什么作用（提示：不同上下文中作用不同）
- 你能想到哪些创建对象的方式
- 如何实现jQuery中的`delegate`方法？（提示，`addEventListener`方法中最后一个参数是什么意思）
- `currentTarget`与`target`有什么区别
- JSONP的原理是什么
- Javascript的基本数据类型有哪些？null和undefined有什么区别？
- 值类型和引用类型有什么区别？（提示：聊聊堆和栈）
- 简述Javascript的垃圾回收机制

#### Javascript代码细节

- 区分Offset/Client/Scroll/Page这几个坐标系
- 区分event.pageX/clientX/screenX
- 表达式(expression) VS 声明(statement)
- (function () {})() VS function fn() {}() VS (function () {})
- `+`运算符的运算规则是怎样的？
- `==` VS `===`
- 比较运算符的比较规则是怎样的？
- `defer` vs `async`

## 答案

### 什么是闭包

> A closure is an inner function that has access to the outer (enclosing) function's variables—scope chain. The closure has three scope chains: it has access to its own scope (variables defined between its curly brackets), it has access to the outer function's variables, and it has access to the global variables

闭包就是内部函数能够访问包裹它的外部函数的作用域链。闭包有三个作用域链：1. 它自己的，2. 访问外部函数的，3. 全局变量。

闭包有一些特性：
1. Closures have access to the outer function’s variable even after the outer function returns:
2. Closures store references to the outer function’s variables;
3. Closures Gone Awry（联想for 操作可能会引起的错误，用Immediately Invoked Function Expression (IIFE)解决）

闭包可以用来构造私有变量，屏蔽细节


### 继承

当一个函数创建时，它就拥有了`prototype`属性。当这个函数作为构造函数创建了一个实例时，这个实例里的`[[Prototype]]`也指向了构造函数的`prototype`属性。当你访问一个实例的属性时，如果实例自己没有，它就会去构造函数的`prototype`属性上查找。所以构造函数的`prototype`对所有实例来说是共有的，修改时要特别小心。如果实例自己和构造函数的`prototype`都有同名属性，则优先返回自己的属性值。判断属性是否是对象的，可以通过`hasOwnProperty`函数。`Object.keys`能够获取对象自己的所有属性名称（不包括prototype上的）。

1. 实现继承最简单的方法是，把构造函数上的`prototype`指向另一个对象，那么当前这个构造函数便继承了另一个对象上的属性以及另一个对象的构造函数上的`prototype`属性
坏处：忘了……有待完善

2. 在子类构造函数中调用想要继承的父类的构造函数方法，又名 Constructor Stealing：
```
function SubType(){ 
    // 修改父构造函数的this指针为自己的
    SuperType.call(this, “Nicholas”);

    //instance property
    this.age = 29;
}
```
坏处：因为调用构造函数，而非new一个。那么方法也必须定义在构造函数内部，prototype上的属性不能共用。

3. 使用`Object.create`方法
```
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}

var person = {
    name: “Nicholas”,
    friends: [“Shelby”, “Court”, “Van”]
};

var anotherPerson = object(person);
```

4. 终极方案，不太记得了，有待补充

### `this`关键词

`this`关键词在不同上下文中含义是不同的
- 在构造函数中表示实例自己
- 在事件的回调函数表示DOM元素
- 在对象的方法中表示该方法所属的对象

### 如何创建对象：

- 字面量(literal): 
```
{
	name: 'Hello'
}
```
- 使用构造函数：
```
new Function()
```
- 为一些特殊的对象创建

### 冒泡与捕获

发生在元素上的事件会经历两个阶段：捕获，冒泡：

捕获，当事件发生在element2上时，实际上element1上的事件处理函数先发生。是否启用捕获，可以通过`addEventListener`的最后一个参数`useCapture`来设置。默认都不启用捕获。
```
               | |
---------------| |-----------------
| element1     | |                |
|   -----------| |-----------     |
|   |element2  \ /          |     |
|   -------------------------     |
|        Event CAPTURING          |
-----------------------------------
```

冒泡：点击事件发生在element2上时，element2上的事件先发生，element1上的事件处理函数后发生
```
               / \
---------------| |-----------------
| element1     | |                |
|   -----------| |-----------     |
|   |element2  | |          |     |
|   -------------------------     |
|        Event BUBBLING           |
-----------------------------------
```
`delegate`方法利用的就是冒泡特性

### `target` VS `currentTarget`

`target`永远指向触发事件的DOM元素
`currentTarget`指向现阶段（冒泡或捕获）的DOM元素，也就是永远和`this`指针相同

### JSONP

JSONP主要解决的是http请求的跨域问题。我们知道http请求是不允许跨域的（即跨端口号，协议，二级域名）。页面上可以跨域请求的资源有外链的脚本和样式。

JSONP的解决之道就是请求一个脚本文件，但是脚本的内容是调用一个函数，函数的参数是请求的数据，比如：
```
callback('{error: 404}');
```
同时本地页面上定义了callback函数，这样本地就能获取到数据了

### 脚本数据类型

基本数据类型只有五种：Number, String, Boolean, Null, Undefined. 如果声明了一个遍历但没有赋值则为undeinfed。null实际上一个空对象的指针，当你把一个变量赋值为null时实际上是让它指向了一个对象，所以你在使用`typeof`测试这个对象类型时会返回 Object

### 引用类型和值类型

- **栈**是一个内存数组，用于存储
    - 某些类型变量的值
    - 程序当前的执行环境
    - 传递给方法的参数

- **堆**是一块内存区域
    - 可以分配大块内存用于存储某种类型的数据
    - 不能显示的删除它们，要依靠CLR(公共语言运行库,可由多种编程语言使用的运行环境, 内存管理、程序集加载、安全性、异常处理和线程同步)的GC机制将他们回收

- 值类型只需要一段单独的内存，用于存储实际的数据

- 引用类型需要两段内存
    - 第一段存储实际的数据，总是位于堆中
    - 第二段是一个引用，指向数据在堆中存放的位置

### 浏览器的常见垃圾回收办法

在javascript中，开发人员是没有办法强制进行垃圾回收的，常见的垃圾回收算法有：

1. Reference Counting: **当某个对象不再被需要**。最早也是最经典的。一个对象是否被回收是由这个对象的被引用次数决定的。对象一旦被创建并被一个变量引用，那么它的引用次数便是1，如果该对象又被赋值给了另一个变量，那么引用便增为 2.一旦某变量删除了对该对象的引用或者另被赋值，那么该对象的引用便又降为1.理论上来说，当一个对象的被引用次数降为0时，表示没有任何变量在引用该对象了，它已经毫无用处可以被回收从内存中释放了。

但可能存在一种两个对象循环引用的情况

2. Mark-and-Sweep: **当某个对象不再能被访问**
- 标记(Mark)：它首先假设存在一些根(root)节点（比如Javascript中的全局对象），从根节点出发，试图去访问其它的每一个与它相连的节点。在Javascript中，如果访问到的节点是基本数据类型(Primitive type)，则对这个节点进行标记，如果不是基本数据类型，也就是Object或者Array，则对这个非基本类型递归这一过程，直到访问到所有节点。
- 清除(Sweep)：经过上面的步骤之后，那么那些存在但没有被标记的对象，则进行回收。

3. 其他回收算法：
    - Two Generational Collector(分代收集算法)
    - Cheney’s algorithm(切尼算法)
    - Mark-compact algorithm(标记压缩算法)

- Offset/Client/Scroll/Page

- `offsetTop/offsetLeft` 相对于containing block/offsetParent的位移
- `offsetHeight/offsetWidth` 元素占用的尺寸（包括border宽度，滚动栏宽度）
- `clientHeight/clientWidth` 元素contnet部分占用的尺寸（包括padding）
- `scrollHeight/scrollWidth` 元素content部分占用的尺寸（在没有滚动条的情况下）
- `scrollTop/scrollLeft` 滚动元素的滚动偏移量
注意clientHeight和scrollHeight很像，但是不同的浏览器处理起来会不同

###  区分event.pageX/clientX/screenX

- pageX/Y gives the coordinates relative to the `<html>` element in CSS pixels.
- clientX/Y gives the coordinates relative to the **viewport** in CSS pixels.
- screenX/Y gives the coordinates relative to the **screen** in device pixels.

### 表达式 VS 声明

表达式和声明最重要的区别是，表达式是一定有返回值的！

### `(function () {})()` VS `function fn() {}()` VS `(function () {})`

- 如果`function foo() {}`是在一个函数体内或者暴露在程序中，那么一定是函数声明
- 如果`function foo() {}`是赋值表达式(`=`)的一部分(assignment expression)，那么它一定是函数表达式
- 如果`function foo() {}`在双括号中(function foo() {})那么一定是表达式，因为`(`和`)`是只能包含表达式的操作符。效果相当于赋值表达式，把定义的函数返回
- `(function () {})()` 创建（返回）了一个函数表达式，并立即执行了这个函数，这种做法名为IIFE(Immediately-Invoked Function Expressions)

### `+`

- 如果左右任意一个操作数(operand)是对象类型(object), 那么会用object-to-primitive算法转化为基本类型，比如使用toString或者valueOf方法
- 转化为基本类型后，如果任意一个操作数是字符串类型，则进行字符串拼接操作
- 否则，转化为数字类型(或者 NaN)，执行加法操作

但是有两个例外：`[] + {} // "[object Object]"`, `{} + [] // 0`

**简单来说，只要任意一个操作数是字符串，优先进行字符串拼接操作**，比如：
- `123 + 'abc'` // "123abc"
- `'abc' + {}`  // "abc[object Object]"

### `==` VS `===`

`==`检测时允许类型转化后进行比较，主要规则如下：

- **如果一个值是数字，另一个值是字符串，把字符串转换为数字，再进行比较**
- 如果一个值为true，将它转化为1再进行比较，反之，false转化为0

注意

- `NaN === NaN //false` NaN永远不会与其他任何值等同
- 比较引用类型，对象，数组，函数时，只有两个变量引用的是同一个对象时，他们才是相等的。

### 比较运算符的规则是怎样的？

- **字符串的比较是采集每个字符在Unicode编码集中的数值**
- **运算数只能转化为数字或者字符串进行比较**
- **如果一个运算数是字符串，另一个运算数是数字，字符串会转化为数字进行比较**
- 如果运算数都不能给转化成数字或字符串，返回false
- 如果某个运算数（被转化成）是NaN，返回false

### `defer VS async`

执行脚本的两大要点：
1. 下载（是否阻塞，是否并行）
2. 执行（是否阻塞，是否顺序）

浏览器当遇到`<script />`标签时，会停止页面的渲染，最糟糕的情况是，逐个下载脚本，并且逐个执行。一段阻塞的脚本意味着直到脚本执行完成页面才能继续渲染。而其中又属请求脚本最耗费时间，每发出一个请求意味着建立连接，dns查找，等待响应。浏览器阻塞的时间几乎等于脚本响应的时间。

- `defer`: 当浏览器遇见带有defer属性的script标签时，开始下载脚本，并且不会阻塞浏览器进程。即使下载完成之后也不会立即执行，直到DOM加载完成之后(DOMContentloaded事件触发时)，或者在onload事件中（但是比onload前一些）
    - defer能够保证脚本按顺序执行
    - defer对缺少src的脚本和动态添加的脚本无效

- `asyc`: async属性与defer属性类似，能够让浏览器同时下载多个脚本而不阻塞浏览器。
不同的是对动态添加的脚本同样有效。并且一旦下载完成后会立即执行，对执行的顺序没有保证: 



- `em` vs `px`

- `getBoundingClietRect` vs `getclientrects`

- `defer` vs `async`

- `parseInt("ff")` vs `parseInt("ff", 16)` and `parseInt(15)` vs `parseInt(15, 16)`

- AMD中`require`与`define`关键字的区别([when to use require and when to use define](http://stackoverflow.com/questions/9507606/when-to-use-require-and-when-to-use-define))

- 假设浏览器在未缩放正常情况下winodw.innerWidth为1280，那么当浏览器页面放大至200%之后，
window.innerWidth/offsetHeighht/点击事件中的pageX/应该是多少？为什么？
