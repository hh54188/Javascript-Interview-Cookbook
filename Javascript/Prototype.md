# Prototype

## FAQ
- 如何创建一个对象？请说出三种以上的方法
- 聊聊prototype属性特性
    - 判断一个属性只存在于prototype上

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
- Parasitic Constructor Pattern：为一些特殊的对象创建构造函数

```
function SpecialArray(){ 
    //create the array
    var values = new Array();

    //add the values
    values.push.apply(values, arguments);

    //assign the method
    values.toPipedString = function(){
        return this.join(“|”);
    };

    //return it
    return values; 
}

- **Durable Constructor Pattern**：与上一个方法类似，但是新增了私有变量的功能，

```
function Person(name, age, job){
    //create the object to return
    var o = new Object();

    //optional: define private variables/functions here
    //attach methods
    o.sayName = function(){
        alert(name);
    }; 

    //return the object
    return o;
}

var friend = Person(“Nicholas”, 29, “Software Engineer”);
friend.sayName(); //”Nicholas”
```

var colors = new SpecialArray(“red”, “blue”, “green”)
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

- 验证对象与prototype之间的关系，可以用`isPrototypeOf`方法：`Person.prototype.isPrototypeOf(person1) // true`

- 获取一个对象的prototype可以用`Object.getPrototypeOf()`

- 注意查找属性的顺序
    - 删除时，只有把同名属性delete掉（置为null不行），才能访问prototype上的同名属性

- `hasOwnProperty`方法：判断该属性是否为实例所有，还是prototype

- `in`、`for...in...`操作符：无论属性在该对象实例上，还是在prototype上，都返回`true`
    - 如何判断一个属性只在prototype上呢？
    - `Object.keys`方法可以取得一个对象的所有属性名称（不包括值，不包括prototype）

- **当重写prototype的属性时，需要注意重写constructor**：

```
function Person(){
}

Person.prototype = {
    constructor: Person,
    name : "Lee"
};

// 否则：
var friend = new Person();
alert(friend instanceof Object); //true 
```

- **prototype是动态的，重写构造函数的prototype时千万小心，可能会影响实例**：与添加prototype属性不同，重写prototype完全是把prototype指向了另一个对象。

- **prototype是被所有对象共享的**