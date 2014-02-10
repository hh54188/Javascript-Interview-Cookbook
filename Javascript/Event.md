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

根据MDN的说法，DOM Level这个概念已经过时了，而是以[DOM Living Standard](http://dom.spec.whatwg.org/)的方式进行维护

[Warning: ](https://developer.mozilla.org/en/docs/DOM_Levels)
>The content of this article may be out of date. The DOM used to be written as a set of levels. That is no longer the case. These days it is maintained as the DOM Living Standard.

## Event Flow