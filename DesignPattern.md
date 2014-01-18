# Design Pattern

## Overview

设计模式永远都在做几件事：

- 把实现和接口**分离**
- 把实现**封装**
- 把接口**抽象**

所以越学你会觉得他们越来越像

**区分的关键是了解并且熟悉他们的使用场景**

## Contents
- Bridge Pattern
- Facade Pattern
- Composite Pattern
- Adapter Patterns

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

## Composite Pattern

>It allows you to treat a collection of objects the same as you would treat any of the
particular sub-objects.

将对象组合成树形结构以表示“部分-整体”的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性

```
function Sentence () {
    this.words = [];
}

// sentence 与 word 是部分与组合的关系，当把sentence删除时
// 实际上是把删除操作传递下去，逐个删除word
Sentence.prototype.remove = function () {
    this.words.forEach(function (word) {
        word.remove();
    })
}
```

## Adapter Patterns

>The adapter pattern allows you to adapt existing interfaces to classes that would otherwise be incompatible. Objects that use this pattern can also be called wrappers,

将一个类的接口转换成客户希望的另外一个接口。该模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。









