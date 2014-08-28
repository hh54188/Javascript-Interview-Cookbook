# DOM

## 目录

- [FAQ](#faq)
- [Node Relationships](#node-relationships)
    - [`insertBefore`/`replaceChild`/`removeChild`；如果只有一个参数用法如何？如何实现`insertAfter()`和`prependChild`?]()
- [Offset Dimensions](#offset-dimensions)
- [Client Dimensions](#client-dimensions)
- [Scroll Dimensions](#scroll-dimensions)
    - [如何区分事件参数中Offset/Client/Scroll/Page这几个坐标系（写一个lazyload）]()
    - [如何区分一个元素的clientHeight/scrollHeight/offsetHeiht等参数]()
- [getBoundingClientRect](#getboundingclientrect)

## FAQ    

- **区分Offset/Client/Scroll/Page这几个坐标系（写一个lazyload即可）**
- 熟悉`insertBefore`等的用法；如果只有一个参数用法如何？如何实现`insertAfter()`/`prepend`?
- `offsetParent` 和 `parentNode` 和 `parentElement` 有什么区别？
- 如何计算一个元素在页面上的位置(Page Coordation:offsetLeft/offsetTop)?
- Offset Dimensions和Client Dimensions哪个是会把scrollbar计算在内？ 
- document.documentElement vs document.body(in Internet Explorer 6 and earlier)

## Node Relationships

![Alt text](images/node-rel.jpg)

### insertBefore(), removeChild(), replaceChild()

**第一个参数永远都是要删除，要插入的新/重要节点，第二个是参考节点**

- `insertBefore()`: `var insertedElement = parentElement.insertBefore(newElement, referenceElement);`

如果第二个referenceElement为空，则如同appendChild

注意，没有`insertAfter方法`，但是可以用`insertBefore`实现, 比如p1想插在p2节点后面：

```
p2.parentNode.insertBefore(p1, p2.nextSibling);

```

利用insertBefore实现prependChild方法：

```
function prependChild(parent, newNode) {
    parent.insertBefore(newNode, parent.firstChild);
}
```

- `removeChild()`: `var oldChild = element.removeChild(child);` **只能删除一级子（儿子）元素，不能删除孙子元素**

删除所有子元素：

```
while (el.firstChild) {
    el.removeChild(el.firstChild);
}
```

- `replaceChild()`: `replacedNode = parentNode.replaceChild(newChild, oldChild)`; 返回的是插入的新节点

- `var dupNode = cloneNode(deep)`: 如果参数为true则拷贝的新节点包括孩子节点，否则只拷贝自己

## Offset Dimensions

http://www.quirksmode.org/dom/w3c_cssom.html#elementview

- offsetHeight: The amount of vertical space, in pixels, taken up by the element, 
including its height, **the height of a horizontal scrollbar (if visible)**, the top border height, 
and the bottom border height.

- offsetWidth: The amount of horizontal space taken up by the element, including its 
width, **the width of a vertical scrollbar (if visible)**, the left border width, and the right 
border width.

- offsetTop: The number of pixels between the element’s outside top border and the 
containing element’s inside top border.

- offsetLeft: The number of pixels between the element’s outside left border and the 
containing element’s inside left border.

- **offsetParent**: When calculating the offsetParent of x the browser moves up the DOM tree to x's ancestors until it encounters one of the following elements. That element becomes x's offsetParent.
    - `<body>`
    - An element with a position other than static.
    - A `<table>`, `<th>` or `<td>`, but only if x has position: static.

### 如何计算一个元素在页面的位置(offsetLeft/offsetTop)

```
function getElementLeft(element){
    var actualLeft = element.offsetLeft;
    var current = element.offsetParent;
    while (current !== null){ 
        actualLeft += current.offsetLeft;
        current = current.offsetParent;
    }
    return actualLeft;
}

function getElementTop(element){
    var actualTop = element.offsetTop;
    var current = element.offsetParent;
    while (current !== null){ 
        actualTop += current.offsetTop;
        current = current.offsetParent;
    }
    return actualTop;
}
```

## Client Dimensions

http://www.quirksmode.org/mobile/tableViewport_desktop.html
http://www.quirksmode.org/dom/w3c_cssom.html

- clientWidth: the width of the content area plus the width of both the left and the right padding.
- clientHeight: the height of the content area plus the height of both the top and the bottom padding.

The client dimensions are literally the amount of space inside of the element,so the **space taken up by scrollbars is not counted**.

Determine the browser viewport size:

```
function getViewport(){

    var viewport = document.documentElement || document.body;
    return {
        width: viewport.clientWidth,
        height: viewport.clientHeight
    }; 
}
```


## Scroll Dimensions

- scrollHeight: The total height of the content if there were no scrollbars present.

- scrollLeft: The number of pixels that are hidden to the left of the content area. This property can be set to change the scroll position of the element.

- scrollTop: The number of pixels that are hidden in the top of the content area. This property can be set to change the scroll position of the element.

- scrollWidth: The total width of the content if there were no scrollbars present.

对document来说，clientWidth和scrollWidth应该是差不多的结果，**但是不同浏览器的实现不同**

- Firefox keeps the properties equal, but the size is related to the actual size of the document content, not the size of the viewport.

- Opera, Safari 3.1 and later, and Chrome keep the properties different, with scrollWidth and scrollHeight equal to the size of the viewport and clientWidth and clientHeight equal to the document content.

- Internet Explorer (in standards mode) keeps the properties different, with scrollWidth and scrollHeight equal to the size of the document content, and clientWidth and clientHeight equal to the viewport size.

**解决方案**

```
var docHeight = Math.max(document.documentElement.scrollHeight, document.documentElement.clientHeight);
var docWidth = Math.max(document.documentElement.scrollWidth, document.documentElement.clientWidth);
```

### 区分事件参数中Offset/Client/Scroll/Page这几个坐标系（写一个lazyload）

在event handler中，存在三个坐标系client（相对于浏览器）、page（相对于页面）、screen（相对于电脑屏幕），这三个坐标都是**鼠标的点击位置**

注意在IE8及之前的浏览器没有page坐标需要算出来:

```
var view = document.documentElement || document.body;
pageX = e.client + view.scrollLeft;
```

一个元素应该有 1.相对于浏览器；2.相对于页面；3.相对于相对父节点(absolute/fixed) 三种坐标

### 如何区分一个元素的clientHeight/scrollHeight/offsetHeight等参数

这三个参数放在一个存在滚动条的容器中最合适

写一个使用保持右下角悬浮组件最合适

- clientHeight：元素用来展现的内容区域高度(包括padding，但不包括滚动条)
- scrollHeight: 导致滚动条产生的滚动区域高度
- offsetHeight: 元素所占的区域高度(包括滚动条)

同时可以参考：http://stackoverflow.com/questions/9027249/confused-by-document-dimensions-in-javascript

注意在再起IE中是没有window.innerWidth这个参数的，需要通过clientHeight来取得。
但是需要判断是否是兼容模式的情况：

```
// 早期的IE中仅仅支持document.body，而不支持document.documentElement
document.compatMode == "BackCompat"? 
    document.body.clientHeight:
    document.documentElement.clientHeight;
```

同时在计算文档高度是也需要注意，clientHeight和scrollHeight可能代表的结果相同也可能不同，
如上面所说。所以需要注意做兼容

```
 Math.max(document.documentElement.scrollHeight, document.documentElement.clientHeight);
```

没事不要用scrollHeight，非常不靠谱


## getBoundingClientRect

几乎所有的浏览器都支持：
http://www.quirksmode.org/dom/w3c_cssom.html

除了一点小意外：

The browser implementations are slightly different. Internet Explorer 8 and earlier consider the upper-left corner of the document to be located at (2,2), whereas the other implementations, including Internet Explorer 9, use the traditional (0,0) as the starting coordinates.