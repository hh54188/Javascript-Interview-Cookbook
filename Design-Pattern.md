# Design Pattern

## Overview

设计模式永远都在做几件事：

- 把实现和接口**分离**
- 把实现**封装**
- 把接口**抽象**

所以越学你会觉得他们越来越像
**区分的关键是了解并且熟悉他们的使用场景**

## Categories Of Design Pattern

- **Creational Design Patterns**: Creational design patterns focus on handling object creation mechanisms where objects are created in a manner suitable for the situation we're working in. 
- **Structural Design Patterns**: Structural patterns are concerned with object composition and typically identify simple ways to realize relationships between different objects. 
- **Behavioral Design Patterns**: Behavioral patterns focus on improving or streamlining the communication between disparate objects in a system.

## 面向对象的五个原则([SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design)))

- **单一职责原则(Single responsibility principle)**： 一个类，最好只做一件事，只有一个引起它的变化
- **开放封闭原则(Open/closed principle)**： 软件实体应该是可扩展的，而不可修改的。也就是，对扩展开放，对修改封闭的。能保持软件内部的封装体系稳定，不被需求的变化影响
- **依赖倒置原则(Dependency inversion principle)**： 具体而言就是高层模块不依赖于底层模块，二者都同依赖于抽象（接口）；抽象不依赖于具体，具体依赖于抽象。
- **接口隔离原则(Interface segregation principle)**： 接口应该是内聚的，应该避免“胖”接口。一个类对另外一个类的依赖应该建立在最小的接口上，不要强迫依赖不用的方法，这是一种接口污染。
- Liskov替换原则(Liskov substitution principle)： 子类必须能够替换其基类。这一思想体现为对继承机制的约束规范，只有子类能够替换基类时，才能保证系统在运行期内识别子类，这是保证继承复用的基础。

**每一次完成一种设计后，应该再想想，如果复用的话是否会违反以上规则**

## Contents
- [Flyweight Pattern](#flyweight-pattern)
- [Proxy Pattern](#proxy-pattern)
- [Observer Pattern](#observer-pattern)
- [Strategy Pattern](#strategy-pattern)
- [Bridge Pattern](#bridge-pattern)
- [Facade Pattern](#facade-pattern)
- [Composite Pattern](#composite-pattern)
- [Adapter Patterns](#adapter-patterns)
- [Decorator Pattern](#decorator-pattern)
- [Factory Pattern](#factory-pattern)

## Flyweight Pattern

享元模式，很简单，就是**Object Pool**！

! http://buildnewgames.com/garbage-collector-friendly-code/
http://blog.sklambert.com/javascript-object-pool/
http://beej.us/blog/data/object-pool/

使用场景：使用的大量的类似对象（比如打飞机游戏中发射的大量子弹），这种情况下引起的问题有：
1. 内存开销太大
2. 内存回收会减低性能

## Proxy Pattern

说到代理模式，就一定要想到迪米特法则：

>迪米特法则（Law of Demeter）又叫作最少知识原则（Least Knowledge Principle 简写LKP），如果两个类不必彼此直接通信，那么这两个类就不应当发生直接的相互作用。如果其中的一个类需要调用另一个类的某一个方法的话，可以通过第三者转发这个调用。

但缺点是：*在系统里造出大量的小方法，这些方法仅仅是传递间接的调用，与系统的业务逻辑无关。*

**代理模式**：为其他对象提供一种代理以控制对这个对象的访问。

*代理对象*和*被代理的对象*都实现同样的接口，这样代理对象就可以替换为被代理对象。

这么做的场景有：

- 安全代理：用于控制真是对象的访问权限
- 智能代理：当调用真实的对象时，代理处理另外一些事，比如计算被引用次数，比如检查是否加锁

## Observer Pattern

当subject的某个动作需要引发一系列不同对象的动作（比如你是一个班长要去通知班里的某些人），与其一个一个的手动调用触发的方法（私下里一个一个通知），不如维护一个列表（建一个群），这个列表存有你想要调用的对象方法（想要通知的人）；之后每次做的触发的时候只要轮询这个列表就好了（群发），而不用关心这个列表里有谁，只用关心想让谁加入让谁退出

这个列表就叫做ObserverList，它有一些维护列表方法：

```
function ObserverList(){
  this.observerList = [];
}
ObserverList.prototype.Add = function( obj ){};
ObserverList.prototype.Empty = function(){};
ObserverList.prototype.Count = function(){};
ObserverList.prototype.Get = function( index ){};
ObserverList.prototype.Insert = function( obj, index ){};
ObserverList.prototype.IndexOf = function( obj, startIndex ){};
ObserverList.prototype.RemoveAt = function( index ){};
```

而我们的subject只用关心两件事：1.维护这个列表，2.发布事件:

```
function Subject(){
  this.observers = new ObserverList();
}

Subject.prototype.AddObserver = function( observer ){
  this.observers.Add( observer );
};  

Subject.prototype.RemoveObserver = function( observer ){
  this.observers.RemoveAt( this.observers.IndexOf( observer, 0 ) );
};  

Subject.prototype.Notify = function( context ){
  var observerCount = this.observers.Count();
  for(var i=0; i < observerCount; i++){
    this.observers.Get(i).Update( context );  
    // 在这里假设的是列表里的每个对象都有update方法，但个人觉得这个列表里也可以是不同对象的不同方法，只要能接受当前上下文作为参数, 可以这样执行：
    // subscription.callback.apply( subscription.context, args );
  }
};
```

这样以来，Subject就不用关心具体的订阅的每一个人，而是只关心列表就好了，而观察者们只要提供一个统一的接口就好了


## Strategy Pattern

- 将不同的算法封装起来，相互之间可以替换，通常会暴露相同的接口(都继承自父类，并实现父类的抽象接口方法)，不至于替换之后会有影响

- 这些算法做的其实都是相同工作，但是内容不同。就好像排序有很多算法，所做的工作都是排序，但是实现不一样（这里不关注效率）

- 而调用者不用关心具体的算法，因为它们都有同样的接口，降低了耦合

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

注意这里要区分桥接模式与外观模式。看上去两者都是封装实现。但细节上是：

1. 桥接模式强调的是**接口与接口实现**的分离
2. 外观模式强调的是屏蔽接口内部实现的复杂，只暴露出接口；使用更高层的接口封装底层的接口

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

文本编辑器可以改变单个字体样式，也可以改变整段字体样式；可以复制单个文件，也可以复制整个文件夹问价；**整体和部分可以被一致对待**

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

>简单的说，需要的东西就在面前，但却不能使用，而短时间又无法改造它，于是我们就想办法适配它

>系统的数据和行为都正常，都接口不符时，我们应该考虑用适配器

```
var Adapter = {
    log: function () {
        console.log(arguments);
    }
}
```

## Decorator Pattern

动态的给一个对象添加一些额外的职责，就增加功能来说，装饰模式比生产子类更为灵活

>They can be used to modify existing systems where we wish to add additional features to objects without the need to heavily modify the underlying code using them.

非常简单的实现：

```
// The constructor to decorate
function MacBook() {
    this.cost = function () { return 997; };
    this.screenSize = function () { return 11.6; };
}
 
// Decorator 1
function Memory( macbook ) {
    var v = macbook.cost();
    macbook.cost = function() {
        return v + 75;
    };
}
 
// Decorator 2
function Engraving( macbook ){
    var v = macbook.cost();
    macbook.cost = function(){
        return  v + 200;
    };
}
 
// Decorator 3
function Insurance( macbook ){
    var v = macbook.cost();
    macbook.cost = function(){
        return  v + 250;
    };
}
 
var mb = new MacBook();
Memory(mb);
Engraving(mb);
Insurance(mb);
```

**注意**，这样的实现是有问题的，它**破坏了开放封闭原则**，直接覆盖了实例的方法。具体实现可以参考`Pro Javscript Desgin Pattern`或者[这里](http://addyosmani.com/resources/essentialjsdesignpatterns/book/#decoratorpatternjavascript);

## Factory Pattern

工厂模式主要解决了对象实例化的问题

重点要区分：

- 简单工厂
- 工厂
- 抽象工厂

>This is particularly useful if the object creation process is relatively complex, e.g. if it strongly depends on dynamic factors or application configuration.

>*工厂方法模式*，定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类

Javascript中没有接口（但是可以用类来代替）！很多的模式在Javascript中都违反了“开放-封闭”的原则，如果要复用就要修改类（或者用类来代替接口）！

>工厂方法模式的实现时，客户端需要决定实例化哪一个工厂来实现运算类，选择判断的问题还是存在的，也就是说，工厂方法把简单工厂的内部逻辑判断移到了客户端代码来进行（这是与**与简单工厂最大的区别**），你想要加的功能，本来是修改工厂类的（**简单工厂方法**），而现在是修改客户端









