# Events

## 目录

- [Event Flow](#event-flow)
- [Bind Event Handler](#bind-event-handler)
- [Event Object](#event-object)
    - [如何区分和理解currenttarget和target](#%E5%A6%82%E4%BD%95%E5%8C%BA%E5%88%86%E5%92%8C%E7%90%86%E8%A7%A3currenttarget%E5%92%8Ctarget)
- [Event Types](#event-types)
    - [如何取得任意一个元素的在页面上的位置(getBoundingClientRect)](#%E5%A6%82%E4%BD%95%E5%8F%96%E5%BE%97%E4%BB%BB%E6%84%8F%E4%B8%80%E4%B8%AA%E5%85%83%E7%B4%A0%E7%9A%84%E5%9C%A8%E9%A1%B5%E9%9D%A2%E4%B8%8A%E7%9A%84%E4%BD%8D%E7%BD%AEgetboundingclientrect)

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

- Internet Explorer 8 and earlier don’t support page coordinates on the event object,The calculation is done as follows:

```
var div = document.getElementById(“myDiv”);
EventUtil.addHandler(div, “click”, function(event){
    event = EventUtil.getEvent(event);
    var pageX = event.pageX,
        pageY = event.pageY;

    if (pageX === undefined){
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

**Screen Coordinates**

### 如何取得任意一个元素的在页面上的位置(getBoundingClientRect)?

**!!! focus & blur**

[Delegating the focus and blur events: A few events, most motably focus, blur, and change, do not bubble up the document tree. ](http://www.quirksmode.org/blog/archives/2008/04/delegating_the.html)

when you define event handlers in the capturing phase,the browser executes any and all event handlers set on ancestors of the event target whether the given event makes sense on these elements or not

Unfortunately IE does not support event capturing, However, it supports the focusin and focusout events that do bubble up

**focus和blur事件在w3c标准中，只能被捕获，而不能冒泡。但是IE不支持捕获，于是它的focusin和focusout是恰恰相反能支持冒泡。所以如果当发现用户绑定的是focusin和focusout时，要当做focus和blur处理**


**!!! mouseover & mouseenter & relatedTarget, fromElement, toElement**

- relatedTarget: This property contains a value only for the mouseoverand mouseoutevents; it is null for all other events.

- fromElement: When the mouseover event fires, Internet Explorer provides a fromElement property containing the related element;

- toElement: when the mouseout event fires, Internet Explorer provides a toElement property containing the related element (Internet Explorer 9 supports all properties).


W3C added the relatedTarget property to mouseover and mouseout events. This contains the element the mouse came from in case of mouseover, or the element it goes to in case of mouseout.

Microsoft created two properties to contain this information:

- `fromElement` refers to the element the mouse comes from. This is interesting to know in case of mouseover.
- `toElement` refers to the element the mouse goes to. This is interesting to know in case of mouseout.

```
body.mouseover = doSomething(e) {
    if (!e) var e = window.event;
    var relTarg = e.relatedTarget || e.fromElement;
}
```
**mouseenter is better than mouseover**

When the mouse cursor moves over the boundary from the parent to the child element, a mouseoutevent is triggered, even though we might consider the cursor to still
be within the bounds of the parent element.

**Solution**:

```
// 不理解
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

**button**

Internet Explorer through version 8 also provides a button property, but it has completely different values, as described here:

- 0 indicates that no button has been pressed.
- 1 indicates that the primary mouse button has been pressed.
- 2 indicates that the secondary mouse button has been pressed.
- 3 indicates that the primary and secondary buttons have been pressed.
- 4 indicates that the middle button has been pressed.
- 5 indicates that the primary and middle buttons have been pressed.
- 6 indicates that the secondary and middle buttons have been pressed.
- 7 indicates that all three buttons have been pressed.

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