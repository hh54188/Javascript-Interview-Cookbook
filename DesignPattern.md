# Design Pattern

## Overview

设计模式永远都在做几件事：

- 把实现和接口分离
- 把实现封装
- 把接口抽象

所以越学你会觉得他们越来越像

**区分的关键是了解并且熟悉他们的使用场景**

## Bridge Pattern

>- **Decoupling abstractions from their implementations allows pieces to be independently managed.**

>- **decouple an abstraction from its implementation so that the two can vary independently.**

桥接模式将抽象（当然包括接口）与实现分离:

```
function getBeerById(id, callback) {
    // Make request for beer by ID, then return the beer data.
    asyncRequest('GET', 'beer.uri?id=' + id, function(resp) {
        // callback response
        callback(resp.responseText);
    });
}

addEvent(element, 'click', getBeerByIdBridge);

function getBeerByIdBridge (e) {
    getBeerById(this.id, function(beer) {
        console.log('Requested Beer: '+beer);
    });
}
```

## Facade Pattern

>**Facades serve to simplify complex interfaces**. They can do error checking for you behind the scenes, clean up large objects that are no longer in use, and present an object’s features in an easier to use fashion. 

外观模式用于封装接口的实现

```
function addEvent(el, type, fn) {
    if (window.addEventListener) {
        el.addEventListener(type, fn, false);
    } else if (window.attachEvent) {
        el.attachEvent('on' + type, fn);
    } else {
        el['on' + type] = fn;
    }
}
```



