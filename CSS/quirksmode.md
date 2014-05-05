http://www.quirksmode.org/css/quirksmode.html
https://www.cs.tut.fi/~jkorpela/quirks-mode.html

## 为什么会有兼容模式(quirks mode)?

>So moving closer to standards compliance would cause problems(change the CSS implementations to match the standards perfectly, many websites would break to a greater or lesser extent. ). On the other hand, not moving closer to standards compliance would perpetuate the general confusion of the Browser Wars Era.

在早期时候，不同浏览器执行的是自己的样式和布局标准，但随着标准化的呼声越来越高，浏览器厂商们陷入了左右为难中：

- 如果按照W3C标准来制作浏览器，那么那些依照自己原有标准的网页将会出现异常
- 如果不按照W3C标准来制作浏览器，浏览器大战将会一直下去

## 解决办法

- 让开发者选择自己执行的标准(比如strict mode)
- 老的页面就是用兼容模式展示(quirks mode)

(X)HTML文档通过`doctype`声明来宣称自己使用的是哪一种模式

- 没有`doctype`意味着使用兼容模式
- 新的或者未知的`doctype`意味着使用标准模式(strict mode)
- 但那些使用兼容模式书写并同时拥有doctype的页面则要具体问题具体分析了：https://hsivonen.fi/doctype/

顺便说一句：通过`doctype`声明来区分页面的呈现(presention)是不科学的，违背了结构与展现的分离，就像css和html一样

