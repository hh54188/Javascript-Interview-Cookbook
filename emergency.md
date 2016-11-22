## 常见的有用函数：

将数组转化为数组：`Array.prototype.slice.call`
检测数据类型：`Object.prototype.toString.call`

## BFC (Block Formatting Context) / haslayout

什么是BFC(Block Formatting Context)，

简单讲，它是提供了一个独立布局的环境，每个BFC都遵守同一套布局规则。例如，在同一个BFC内，盒子会一个挨着一个的排，相邻盒子的间距是由margin决定且垂直方向的margin会重叠。而float和clear float也只对同一个BFC内的元素有效。

如何触发BFC：

非块级盒子的浮动元素、绝对定位元素及块级容器(比如inline-blocks，table-cells和table-captions)，以及overflow属性是visible之外任意值的块级盒子，都会创建了一个BFC。即当元素CSS属性设置了下列之一时，即可创建一个BFC:
- float：left | right
- position：absolute | fixed
- display: table-cell | table-caption | inline-block
- display: table-cell | table-caption | inline-block

BFC解决的问题：

- 阻止右侧文字围绕左侧的浮动块，解决办法开启文字的BFC属性，用`overflow:hidden`
- 消除因为子元素浮动引起的折叠, 解决办法同上
- 创建了 BFC的元素不会与它们的子元素发生外边距折叠

## position:

- relative: 别人以为它还在标准流中；无法设置z-index
- absolute：相对于距离它最近的position不为static的祖先元素。

## Containing Block

- 根元素（我不理解为什么没有明确指出是body或html）的包含块被称为初始包含块(initial containing block)，这个包含块指的是浏览器视口(viewport)，就是浏览器的窗口大小。
- 对position为relative或static的元素来说，包含块指1.最近的;2.块级祖先元素；3.内容边缘(content edge)所形成的区域（这一条从正面来说比较好理解，但是我还没有找到这样的一个反例）
- position为fixed的元素，包含块就是视口
- 如果元素是绝对定位，包含块除了要满足具有出static之外的定位属性意外，还要分一下两种情况:
	- 当满足情况的祖先元素是行内元素时，包含块应该是上一节绝对定位中最后描述的，行内元素多行时，当右侧参差不齐时形成区域的规则。在CSS2.1中，如果行内元素被分为许多行，那么包含块为undefined
	- 如果不是行内元素，包含块即是那个祖先元素内边距边缘所形成的区域。
- 最后，如果该元素都不满足上述任何条件，那么包含块即是指“初始包含块”


## 变量和函数声明

关键在于javascript的变量声明有一个hoisting机制，变量声明永远都会被提升至作用域的最顶端（注意只是声明，还没有赋值）。

```
var a = 10;
(function () {
	console.log(a); 
	var a = 20;
})()

// 运行结果为 undefined
```

因为以上代码相当于：

```
var a = 10;
(function () {
	var a; //在这里对变量hoisting，先声明
	console.log(a); 
	a = 20; //再赋值
})()
```

函数声明会覆盖变量声明

```
function value(){
    return 1;
}
var value;
alert(typeof value);    //"function"
```

函数声明不会覆盖变量赋值或者说初始化

```
function value(){
    return 1;
}
var value = 1;
alert(typeof value);    //"number"
```

因为存在 Name Resolution Order

在javascript中，一个变量名(name)有四种方式进入作用域(scope)中：

- 语言内置，所有的作用域中都有this和arguments关键字
- 形式参数，函数的参数在整个作用域中都是有效的
- 函数声明
- 变量声明

上面列出的四种顺序也正是由高到底的优先级的顺序，一旦一个变量名已经声明了，那么它就不可能被其他更低优先级的变量声明形式所覆盖。
注意，这个说法可能有误。在函数体中重新声明的`arguments`和形参是可以覆盖原始的



