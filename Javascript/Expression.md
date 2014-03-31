# Expression

## FAQ 

- 有哪些运算符是由关键字表示的
- 算术运算符在什么情况下可以将字符转化成数字？
- `==`(等同)与`===`(相等)有什么区别？
- `==`下，不同类型进行比较的规则是什么？
- `"1" == true`?
- 赋值运算符有什么特征？

## 主要运算符优先级：

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Operator_precedence

<table>
    <tr><td>member</td><td>  . []</td></tr>
    <tr><td>call / create instance</td><td>  () new</td></tr>
    <tr><td>negation/increment</td><td>  ! ~ - + ++ -- typeof void delete</td></tr>
    <tr><td>multiply/divide</td><td> * / %</td></tr>
    <tr><td>addition/subtraction</td><td>    + -</td></tr>
    <tr><td>bitwise shift</td><td>   << >> >>></td></tr>
    <tr><td>relational</td><td>  < <= > >= in instanceof</td></tr>
    <tr><td>equality</td><td>    == != === !==</td></tr>
    <tr><td>bitwise-and</td><td> &</td></tr>
    <tr><td>bitwise-xor</td><td> ^</td></tr>
    <tr><td>bitwise-or</td><td>  |</td></tr>
    <tr><td>logical-and</td><td> &&</td></tr>
    <tr><td>logical-or</td><td>  ||</td></tr>
    <tr><td>conditional</td><td> ?:</td></tr>
    <tr><td>assignment</td><td>  = += -= *= /= %= <<= >>= >>>= &= ^= |=</td></tr>
    <tr><td>comma</td><td>   ,</td></tr>
</table>

## `==` vs `===`

**`!=` 与 `==` 同样可以参考此街规则**


- `===`:等同运算符，它采用严格的同一性定义检测
- `--`:相等运算符，它采用比较宽松的同一性定义（即允许类型转换）

|

- 比较引用类型，对象，数组，函数时，只有两个变量引用的是同一个对象时，他们才是相等的。

|

- `NaN === NaN //false` NaN永远不会与其他任何值等同
- `true === true //true`
- `null === null //true`
- `undefined === undefined //true`

|

- `null == undefined // true`
- **如果一个值是数字，另一个值是字符串，把字符串转换为数字，再进行比较**
- **如果一个值为true，将它转化为1再进行比较，反之，false转化为0**
- `"1" == true // true`

## 比较运算符

- 运算数只能转化为数字或者字符串进行比较
- 如果一个运算数是字符串，另一个运算数是数字，字符串会转化为数字进行比较
- 如果运算数都不能给转化成数字或字符串，返回false
- 如果某个运算数（被转化成）是NaN，返回false

|

- 字符串的比较是采集每个字符在Unicode编码集中的数值

## 赋值运算符`=`

- 它将右边的值赋给左边的变量
- 结合性是从右到左
- 有返回值

## 其他

- `instanceof` 要求左边是一个**对象** `var a = 1; a instanceof Number; // false`
- `+` 字符串操作符，如果两个运算数之一是字符串，那么另一个也会被转化为字符串，执行连接操作
    - 1 + 2 + " bind mice" // 3 bind mice
    - "bind mice" + 1 + 2 // bind mice 12
- `typeof`: 运算数是Number、String、Boolean这样的包装对象时，它返回`Object`
- `?:`
- `new`
- `delete`: 它将删除运算数指定的对象的属性，数组元素或变量。如果成功返回true、失败返回false
- **`void`**: `void`比`undefined`先出生，用表达式`void 0`比`undefined`更有用
- **`,`**: 先计算其左边的参数，再计算其右边的参数，然后返回最右边的参数