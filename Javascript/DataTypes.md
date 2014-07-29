# Data Types

## FAQ

- 哪些是基本类型，哪些是引用类型
- 引用类型和值类型在内存存储上有哪些不同？
- 如何表示十六进制和八进制？如何将十六进制八进制转化为十进制？
- 如何把一个数字转为字符串？或者把字符串转为数字？(toFixed, parseInt)
- `var number = string_value - 0` 与 `var number = string_value + 0`有什么区别？
- 区别`parseInt("ff")`与`parseInt("ff", 16)`、`parseInt(15)`与`parseInt(15, 16)`
- 哪些数字和字符串和哪些值可以转化为false？

## Contents

- [typeof && instanceof](#typeof--instanceof)
- [Primitive](#primitive)
- [Reference Types](#reference-types)
- [Argument passing](#argument-passing)

## typeof && instanceof

>Although typeofworks well for primitive values, it’s of little use for reference values

>result = variable instanceof constructor

>The instanceof operator returns true if the variable is an instance of the given reference type 

**Something wrong with `typeof`**:

- `typeof new Number // object`
- `typeof null // object`
- `typeof NaN // number`

**With one exception**

- `typeof new Function // function `
- `typeof function () {}` // function`

**The difference between `String value(primitive string)` and [String object](http://es5.github.io/#x4.3.16)**

- A String object is created by using the String constructor in a new expression, supplying a String value as an argument
- A String object can be coerced to a String value by calling the String constructor as a function:
    - `typeof new String("a") // object`
    - `typeof String(new String("a")) // string`

**Object.prototype.toString.call()**

- `Object.prototype.toString.call(n) // [object number]`

## 引用类型和值类型在内存存储上有哪些不同

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


## Primitive

- Boolean
    - 数字：0或者NaN可转化为false，其他的统统转为true
    - 字符串：空字符串转化为false
    - 空值或未定义转化为false
    - 空对象，空数组或函数都为true
- Number
    - 十六进制以0x开头，八进制以0开头
    - 十六进数2AF5, 换算成10进制：5 x 16^0 + 15 x 16^1 + 10 x 16^2 + 2 x 16^3
    - 把数字转化为字符串，把字符串转化为数字
    - 区别`parseInt("ff")`与`parseInt("ff", 16)`、`parseInt(15)`与`parseInt(15, 16)`
- String
- Undefined
    - When a variable is 
declared using varbut not initialized, it is assigned the value of undefined
    - When compared with the 
literal value of undefined, the two are equal
    - a variable containing the value of undefinedis different from a variable that hasn’t been 
defined at all
    - The typeofoperator returns “undefined”when called on an uninitialized variable, but it also 
returns “undefined”
- Null
    - **A null value is an empty object pointer(empty object reference), which is why `typeof`returns “object” when it’s passed a `null` value** `var car = null; alert(typeof car); //”object”`
    - you should never explicitly set the value of a variable to undefined
    - Any time an object is expected but is not available, nullshould be 
used in its place
    - `null == undefined // true` 

## Reference Types

以下这些数据类型，都可以被看做是对象，数组其实是一种特殊的对象

- Object
- Array
- Date
- RegExp
- Function
- Primitive Wrapper Types(String Object/Number Object/Boolean Object):

    Professional Javascript for Web Developers, 3ed Edition: Chapter 5: Reference Types: Primitive Wrapper Types:

    ```
    var s1 = “some text”;
    var s2 = s1.substring(2);
    ```

        In this code, s1is a variable containing a string, which is a primitive value. On the next line, the 
    substring()method is called on s1and stored in s2. Primitive values aren’t objects, so logically 
    they shouldn’t have methods, though this still works as you would expect. In truth, there is a lot 
    going on behind the scenes to allow this seamless operation. When s1is accessed in the second line, 
    it is being accessed in read mode, which is to say that its value is being read from memory. Any time 
    a string value is accessed in read mode, the following three steps occur:

    1. Create an instance of the Stringtype.
    2. Call the specified method on the instance.
    3. Destroy the instance.

    You can think of these three steps as they’re used in the following three lines of ECMAScript code:
    ```
    var s1 = new String(“some text”);
    var s2 = s1.substring(2);
    s1 = null;
    ```
    This behavior allows the primitive string value to act like an object.



## Argument Passing

All function arguments in ECMAScript are passed by value. This means that the value outside of 
the function is copied into an argument on the inside of the function the same way a value is copied 
from one variable to another