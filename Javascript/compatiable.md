
## Node type 

```
if (someNode.nodeType == Node.ELEMENT_NODE){ //won’t work in IE < 9
    alert(“Node is an element.”);
}

if (someNode.nodeType == 1){ //works in all browsers
    alert(“Node is an element.”);
}
```


## focus && blur

[A few events, most motably focus, blur, and change, do not bubble up the document tree. ](http://www.quirksmode.org/blog/archives/2008/04/delegating_the.html)

when you define event handlers in the capturing phase,the browser executes any and all event handlers set on ancestors of the event target whether the given event makes sense on these elements or not

Unfortunately IE does not support event capturing, However, it supports the focusin and focusout events that do bubble up

**focus和blur事件在w3c标准中，只能被捕获，而不能冒泡。但是IE不支持捕获，于是它的focusin和focusout是恰恰相反能支持冒泡。所以如果当发现用户绑定的是focusin和focusout时，要当做focus和blur处理**


## mouseover && mouseenter

**relatedTarget, fromElement, toElement**

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

