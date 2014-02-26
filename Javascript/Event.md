# Events

## 目录

- [FAQ](#faq)
- [Event Flow](#event-flow)
- [Bind Event Handler](#bind-event-handler)
- [Event Object](#event-object)
    - [如何区分和理解currenttarget和target](#%E5%A6%82%E4%BD%95%E5%8C%BA%E5%88%86%E5%92%8C%E7%90%86%E8%A7%A3currenttarget%E5%92%8Ctarget)
- [Event Types](#event-types)
    - [如何取得任意一个元素的在页面上的位置(getBoundingClientRect)](#%E5%A6%82%E4%BD%95%E5%8F%96%E5%BE%97%E4%BB%BB%E6%84%8F%E4%B8%80%E4%B8%AA%E5%85%83%E7%B4%A0%E7%9A%84%E5%9C%A8%E9%A1%B5%E9%9D%A2%E4%B8%8A%E7%9A%84%E4%BD%8D%E7%BD%AEgetboundingclientrect)
    - [如何修复event.which用于表达鼠标的正确位置](#%E5%A6%82%E4%BD%95%E4%BF%AE%E5%A4%8Deventwhich%E7%94%A8%E4%BA%8E%E8%A1%A8%E8%BE%BE%E9%BC%A0%E6%A0%87%E7%9A%84%E6%AD%A3%E7%A1%AE%E4%BD%8D%E7%BD%AE)
    - [如何区分focus/blur/focusin/focusout](#focus--blur--focusin--focusout)
    - [如何区分mouseover/mouseout/mouseenter/mouseleave](#mouseover--mouseenter--relatedtarget-fromelement-toelement)
- [如何模拟DOMContentLoaded事件](#%E5%A6%82%E4%BD%95%E6%A8%A1%E6%8B%9Fdomcontentloaded%E4%BA%8B%E4%BB%B6)



## FAQ

- 有哪些绑定事件的方式，聊聊有什么异同(冒泡，this，event type)
- IE的事件类型和其它浏览器有什么不同（抛开版本谈都是耍流氓？）
- 如何区分和理解currenttarget和target
- 如何取得任意一个元素的在页面上的位置(Page Coordination)
- 如何区分 focus/blur/focusin/focusout
- 如何区分 mouseover/mouseout/mouseenter/mouseleave
- 如何模拟DOMContentReady事件

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

- currentTarget: 当前执行的是谁的event handler，谁就是currentTarget（冒泡或捕获过程中的元素） ** the `this` object is always equal to the value of currentTarget**

- target: 最初触发事件的元素

- preventDefatult(): 执行该函数时会导致  cancelable 变为true

- stopPropagation(): 执行该函数时会导致 bubblesis 变为 true

- stopImmediatePropagation(): Cancels any further event capturing or event bubbling and **prevents any other event handlers from being called**.(Added in DOM Level 3 Events.)

**The Internet Explorer Event Object**

- cancelBubble: can be set to true to cancel event bubbling
- returnValue: can be set to false to cancel the default behavior of the event
- srcElement: same as the DOM target property

IE中是没有currentTarget参数，具体可以参考jQuery实现，即把回调函数中的this赋值给currentTarget `event.currentTarget = this`

The event handling function is referenced, not copied, **so the this keyword always refers to the window and is completely useless.**

### 如何区分和理解currentTarget和target？

假设页面上有一个按钮，同时给button和body绑定click事件,当点击按钮时：

```
var btn = document.getElementById(“myBtn”);
btn.onclick = function(event){
    // the this object is always equal to the value of currentTarget
    // currentTarget总是与this关键字等价
    // 而this总是指向当前函数属于的dom
    alert(event.currentTarget === this); //true 
    alert(event.target === this); //true
};

// 冒泡被触发
document.body.onclick = function(event){
    alert(event.currentTarget === document.body); //true
    alert(this === document.body); //true
    alert(event.target === document.getElementById(“myBtn”)); //true
};
```


## Event Types

**load**

- Note that the element need not be added to the document for the image download to begin; it begins as soon as the srcproperty is set.

- JavaScript files start downloading only after the srcproperty has been assigned and **the element has been added into the document**

**unload**

- Since the unloadevent fires after everything is unloaded, not all objects that were available when the page was loaded are still available. **Trying to manipulate the location of a DOM node or its appearance can result in errors.**

**resize**

- Explorer, Safari, Chrome, and Opera fire the resize event as soon as the browser is resized by one pixel and then repeatedly as the user resizes the browser window.

- Firefox fires the resize event 
only after the user has stopped resizing the browser. 

**Scroll**

- In quirks mode, the changes are observable using the `scrollLeft` and 
`scrollTop` of the `<body>` element;

- In standards mode, the changes occur on the `<html>`element in 
all browsers

- (In standards mode)Safari which still tracks scroll position on `<body>`.

**Client Coordinates**

**Page Coordinates**

- The values for pageX and pageY are the same as clientX and clientY when the page is not scrolled.

- IE8与之前的IE浏览器都没有这个属性Internet Explorer 8 and earlier don’t support page coordinates on the event object,The calculation is done as follows:

```
var div = document.getElementById(“myDiv”);
EventUtil.addHandler(div, “click”, function(event){
    event = EventUtil.getEvent(event);
    var pageX = event.pageX,
        pageY = event.pageY;

    if (pageX === undefined){

        // document.documentElement: Returns the Element that is the root element of the document
        // document.body: In documents with <body> contents, returns the <body> element, and in frameset documents, this returns the outermost <frameset> element.

        pageX = event.clientX + (document.body.scrollLeft || 
        document.documentElement.scrollLeft);
    }

    if (pageY === undefined){
        pageY = event.clientY + (document.body.scrollTop || 
        document.documentElement.scrollTop);
    }

    alert(“Page coordinates: “ + pageX + “,” + pageY); 
});
```

jquery 1.3.2 的方法：

```
// Calculate pageX/Y if missing and clientX/Y available
if ( event.pageX == null && event.clientX != null ) {
    var doc = document.documentElement, body = document.body;
    // 为什么要减clientLeft?
    event.pageX = event.clientX + (doc && doc.scrollLeft || body && body.scrollLeft || 0) - (doc.clientLeft || 0);
    event.pageY = event.clientY + (doc && doc.scrollTop || body && body.scrollTop || 0) - (doc.clientTop || 0);
}
```

**Screen Coordinates**

### 如何取得任意一个元素的在页面上的位置(getBoundingClientRect)?

getBoundingClientRect返回的是一个元素在页面上的client坐标系（相对于viewport/浏览器而言）

**兼容IE6** http://www.quirksmode.org/dom/w3c_cssom.html#t21

Returns an object that contains the top, left, right, and bottom (all relative to the top left of the viewport) of the combined rectangle of element x. Essentially, the browser calculates all rectangles (see below getClientRects()), and getBoundingClientRect() returns the lowest (top, left) or highest (bottom, right) values found.

联想getClientRects，兼容IE6，但是在IE6下表现和W3C不一致

https://developer.mozilla.org/en-US/docs/Web/API/Element.getClientRects
http://www.quirksmode.org/dom/w3c_cssom.html#t22

### focus & blur & focusin & focusout

http://www.quirksmode.org/dom/events/index.html#t08
http://www.quirksmode.org/blog/archives/2008/04/delegating_the.html

这四个事件从IE6起的所有浏览器都支持，区别是

- focus和blur（还有change）事件在w3c标准中，只能被捕获，而不能冒泡
- 但是IE不支持捕获，于是它的focusin和focusout是恰恰相反能支持冒泡。

- [bug] focusin/focusout: Safari and Chrome fire these events only with addEventListener; not with traditional registration(`el.onfocusin`).
- [bug] focus/blur: Firefox Mac, Safari, and Chrome sometimes don’t support these events on links and/or form fields. See detail page for bug descriptions

jQuery会模拟冒泡事件
或者把所有的focusin/focusout事件当做focus或者blur来处理


### mouseover & mouseenter & relatedTarget, fromElement, toElement

http://www.quirksmode.org/js/events_mouse.html
http://www.quirksmode.org/blog/archives/2008/04/delegating_the.html

mouseover, mouseout, mouseenter, mouseleave 理论上从IE6开始的所有浏览器都支持，区别：

- mouseover/mouseout: 支持冒泡，无论从子元素向该元素移动，还是从父元素向该元素移动，都会触发
- mouseenter/mouseleave: 不支持冒泡，仅当从元素外部（不仅仅是父元素，还可能是祖父元素）向该元素移动时，才会触发

当然 mouseenter/mouseleave 更加合理

**mouseenter is better than mouseover**

When the mouse cursor moves over the boundary from the parent to the child element, a mouseout event is triggered, even though we might consider the cursor to still be within the bounds of the parent element.

如果鼠标只是在元素内部移来移去，当然不希望触发元素的mouseover和mouseleave事件了

```
// 如何解决这个问题
// Secrets of the Javascript Ninja: page 323

var hover = function (elem, fn) { 
    addEvent(elem, "mouseover", function (e) {
        withinElement(this, e, "mouseenter", fn);
    });
    addEvent(elem, "mouseout", function (e) {
        withinElement(this, e, "mouseleave", fn);
    });
};

function withinElement(elem, event, type, handle) { 
    var parent = event.relatedTarget; 
    while (parent && parent != elem) { 
        try {
            parent = parent.parentNode;
        } catch (e) { 
            break;
        }
    }

    if (parent != elem) { 
        handle.call(elem, type);
    }
}
```

这个解决方案的原理是模拟mouseenter，当一个mouseover触发的条件仅允许，鼠标从该元素外部向该元素移动，所以在父元素和祖先元素无论如何也不能匹配上元素本身，如果在内部触发就不一样了，不停的网上查找一定会匹配到元素本身的

- [W3C] relatedTarget: This property contains a value only for the mouseover and mouseout events; it is null for all other events.

- [IE] fromElement: When the mouseover event fires, Internet Explorer provides a fromElement property containing the related element;

- [IE] toElement: when the mouseout event fires, Internet Explorer provides a toElement property containing the related element (Internet Explorer 9 supports all properties).


W3C added the `relatedTarget` property to `mouseover` and `mouseout` events. This contains the element the mouse came from in case of mouseover, or the element it goes to in case of mouseout.

Microsoft created two properties to contain this information:

- `fromElement` refers to the element the mouse comes from. This is interesting to know in case of mouseover.
- `toElement` refers to the element the mouse goes to. This is interesting to know in case of mouseout.

```
body.mouseover = doSomething(e) {
    if (!e) var e = window.event;
    var relTarg = e.relatedTarget || e.fromElement;
}
```

**button**

Internet Explorer through version 8 also provides a button property, but it has completely different values, as described here:

- 0 indicates that no button has been pressed.
- **1 indicates that the primary mouse button has been pressed.**
- **2 indicates that the secondary mouse button has been pressed.**
- 3 indicates that the primary and secondary buttons have been pressed.
- **4 indicates that the middle button has been pressed.**
- 5 indicates that the primary and middle buttons have been pressed.
- 6 indicates that the secondary and middle buttons have been pressed.
- 7 indicates that all three buttons have been pressed.

### 如何修复event.which用于表达鼠标的正确位置

IE9以下的浏览器不支持event.which，但是支持event.button，但event.button的参数又和其它浏览器的参数不一样

（真正的event.which返回的其实是charCode）

**jQuery 1.3.2 line 2753**

```
// Add which for click: 1 == left; 2 == middle; 3 == right
// Note: button is not normalized, so don't use it
if ( !event.which && event.button )
    event.which = (event.button & 1 ? 1 : ( event.button & 2 ? 3 : ( event.button & 4 ? 2 : 0 ) ));
```

Internet Explorer provides the following additional information for each mouse event as well:

- altLeft is a Boolean value indicating if the left Alt key is pressed. If altLeft is true then altKey is also true.
- ctrlLeft is a Boolean value indicating if the left Ctrl key is pressed. If ctrlLeft is true then ctrlKeyis also true.
- offsetXis the x-coordinate of the cursor relative to the boundaries of the target element.
- offsetYis the y-coordinate of the cursor relative to the boundaries of the target element.
- shiftLeft is a Boolean value indicating if the left Shift key is pressed. If shiftLeft is true, then shiftKey is also true.

**Key Codes**

the eventobject’s keyCodeproperty is filled in with a code that 
maps to a specific key on the keyboard.

For alphanumeric keys, the keyCode is the same as the ASCII value for the lowercase letter or number on that key, so the 7 key has a keyCodeof 55 and the A key has a keyCodeof 65, regardless of the state of the Shift key.

**Character Codes**

**When a keypressevent occurs, this means that the key affects the display of text on the screen. ** Internet Explorer 9+, Firefox, Chrome, and Safari support a property on the eventobject called 
charCode, which is filled in only for the keypressevent and contains the ASCII code for the 
character related to the key that was pressed.

- key(DOM Level 3): When a character key is pressed, the value of keyis equal to the text character (for example, “k” or “M”); when a noncharacter key is pressed, the value of keyis the name of the key (for example, “Shift” or 
“Down”)

- char(DOM Level 3): The charproperty behaves the same as keywhen a character key is pressed and is set to 
nullwhen a noncharacter key is pressed.

- location(DOM Level 3):which is a numeric value indicating where the key was pressed.

## 如何模拟DOMContentLoaded事件

http://qingbob.com/talk-about-domcontentloaded-technical/

**no-IE**

轮询document的readyState属性，当值为loaded或者complete时
或
直接注册DOMContentloaded

**IE**

1. 在页面临时插入一个script元素，并设置defer属性，最后把该脚本加载完成视作DOMContentLoaded事件来触发。
但这样做有一个问题是，如果插入脚本的页面包含iframe的话，会等到iframe加载完才触发

2. 通过setTiemout来不断的调用documentElement的doScroll方法，直到调用成功则出触发DOMContentLoaded