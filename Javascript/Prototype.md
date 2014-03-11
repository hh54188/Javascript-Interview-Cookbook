# Prototype

## FAQ
- 如何创建一个对象？请说出三种以上的方法

## 目录

## Type of Object Properties

- Data properties
    - [[Configurable]]
    - [[Enumerable]]
    - [[Writable]]: if the property’s value can be changed
    - [[Value]]

**Object.defineProperty()** 定义属性，并且能自定义*属性的属性*

```
var person = {};
Object.defineProperty(person, “name”, {
    configurable: false,
    value: “Nicholas”
});
```

- Accessor properties(没有实际的值，而是要借用Get或者Set方法取值):
    - [[Configurable]]
    - [[Enumerable]]
    - [[Get]]
    - [[Set]]

**Object.defineProperty():**

```
var book = {
    _year: 2004,
    edition: 1
};

Object.defineProperty(book, “year”, {
    get: function(){
        return this._year;
    },
    set: function(newValue){
        if (newValue > 2004) {
            this._year = newValue;
            this.edition += newValue - 2004;
        }
    }
});
```
或者：

```
var book = {
    _year: 2004,
    edition: 1
};

//legacy accessor support
book.__defineGetter__(“year”, function(){
    return this._year; 
});

book.__defineSetter__(“year”, function(newValue){
    if (newValue > 2004) {
        this._year = newValue;
        this.edition += newValue - 2004;
    } 
});
```

**Object.defineProperties()** 批量定义属性，同时能够操控*属性的属性*

```
var book = {};
Object.defineProperties(book, {
    _year: {
        value: 2004
    },
    edition: {
        value: 1
    },
    year: { 
        get: function(){
            return this._year;
        },
        set: function(newValue){
            if (newValue > 2004) {
                this._year = newValue;
                this.edition += newValue - 2004;
            } 
        } 
    } 
});
```

## 如何创建一个对象

- Factory Pattern:

```
function createPerson(name){
    var o = new Object();
    o.name = name;
}
```

- The Constructor Pattern

```
function Person(name) {
    this.name = name;
}

// 同时要区别这三种用法：

//use as a constructor
var person = new Person("lee");

//call as a function
Person("lee"); //adds to window

//call in the scope of another object
var o = new Object();
Person.call(o, "lee");

```

- The Prototype Pattern

```
function Person(){
}
Person.prototype.name = "lee";
```

## 如何理解Prototype属性

- 当一个函数创建时，它也就拥有了`prototype`属性。

- 所有的prototype属性都有一个名为constructor的属性，指向拥有prototype的函数。

- 每当用构造函数创建一个实例时，实例的[[Prototype]]属性便指向构造函数的prototype属性。

- 每一个对象都有一个`__proto__`的属性，表示**实例**与**构造函数的prototype属性**之间的关系（而并非实例与构造函数之间的关系）