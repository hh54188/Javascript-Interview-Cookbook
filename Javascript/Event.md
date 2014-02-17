# Events

有哪些是冷门同时也是重点？

- 使用联系DOM level 0, DOM level 1, DOM level 2来谈?
- 冒泡、捕获的细节问题：所有事件都有冒泡？冒泡的最终对象都是html？
- 绑定event handler的方式有哪一些？彼此之间有哪些差异？
- Event Object
    - IE与一般方式有哪些异同？
    - **从修复Event Object谈起**（引申到存在处理异同的Event），这一部分参考John的忍者书

## DOM Levels

一般可以理解为：

[What are DOM levels?](http://stackoverflow.com/questions/6629093/what-are-dom-levels#answer-6629137)

>DOM Levels are the versions of the specification for defining how the Document Object Model should work, similarly to how we have HTML4, HTML5, and CSS2.1 specifications.

维基百科参考：

[Document Object Model - Standardization](http://en.wikipedia.org/wiki/Document_Object_Model#Standardization)

根据MDN的说法，DOM Level这个概念已经过时了，而是以[DOM Living Standard](http://dom.spec.whatwg.org/)的方式进行维护。当然这是WHATWG的说法，他们希望有一个随时可以被编辑的标准。而W3C不这么认为。

[Warning: ](https://developer.mozilla.org/en/docs/DOM_Levels)
>The content of this article may be out of date. The DOM used to be written as a set of levels. That is no longer the case. These days it is maintained as the DOM Living Standard.

## Event Flow

事件的流程包括**捕获**和**冒泡**阶段。我们在这里要关注几个细节。

- 最开始，Netscape只支持捕获，IE(6?)只支持冒泡

- IE9，包括所有的现代浏览器，**冒泡的终点的应该是windows对象**。换而言之，如果要使用delegate的话，可以直接注册在windows对象上，而避免与DOM打交道（IE6、7、8表现如何？）；

- **现代浏览器捕获的起点是`document`**，而不是windows

- 虽然DOM Level2很明确的表示在捕获阶段不会触发event target（也就是说触发event target应该算是冒泡阶段的一部分），但是很多浏览器还是会在捕获阶段触发event target（**应该怎么理解这一段？**）

## Bind Event Handler

**Html Event Handlers**

```
<input type=”button” value=”Click Me” onclick=”alert(‘Clicked’)” />
```

**DOM Level 0 Event Handlers**

```
btn.onclick = fn
```

- It’s possible to use thisto access any of the element’s properties or methods from within the event 
handlers. 

- Event handlers added in this way are intended for the bubbling phase of the event flow.(**这样绑定的函数默认是在冒泡阶段触发！**)；

**DOM Level 2 Event Handlers**

```
btn.addEventListener(“click”, fn, false);
```

**Internet Explorer Event Handlers**

```
btn.attachEvent(“onclick”, function(){
    alert(this === window); //true
});
```

- when using attachEvent(), the event handler runs in the global context, so `this` is equivalent to `window`.

## Event Object

**DOM Event Object**

- currentTarget: 当前执行的是谁的event handler，谁就是currentTarget（冒泡或捕获过程中的元素）

- target: 最初触发事件的元素

- preventDefatult(): 执行该函数时会导致  cancelable 变为true

- stopPropagation(): 执行该函数时会导致 bubblesis 变为 true

- stopImmediatePropagation(): Cancels any further event capturing or event bubbling and **prevents any other event handlers from being called**.(Added in DOM Level 3 Events.)

**The Internet Explorer Event Object**

- cancelBubble: can be set to true to cancel event bubbling
- returnValue: can be set to false to cancel the default behavior of the event
- srcElement: same as the DOM target property

The event handling function is referenced, not copied, **so the this keyword always refers to the window and is completely useless.**