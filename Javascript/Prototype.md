# Prototype

## FAQ
- 说出一些ECMAScript 5的Object方法，比如`Object.keys`
- 请详细解释属性的属性`[[Configurable]]`, `[[Enumerable]]`, `[[Writable]]`, `[[Value]]`
- 如何创建一个对象？请说出三种以上的方法
- 聊聊prototype属性特性
    - 判断一个属性只存在于prototype上
- 请列举出至少三种的继承方式，并说明他们的优劣

## 目录

## 一些新的Object方法：

http://ejohn.org/blog/ecmascript-5-objects-and-properties/#postcomment

- `Object.keys()`: returns an array of a given object's **own** **enumerable** properties

- `Object.create()`: （创建）返回一个对象，该对象的prototype属性即为传入对象

- `Object.getOwnPropertyNames()`: returns an array of all properties **(enumerable or not)** found **directly upon** a given object.

- `Object.getOwnPropertyDescriptor()`:  returns a property descriptor for an own property

- `Object.freeze()`: 该对象的属性不可以被修改，也不可新增属性enumerability, configurability, or writability都不可以被修改

- `Object.seal()`: 不可新增属性，但旧属性仍然可被修改

- `defineProperties()`

- `defineProperty()`

## Type of Object Properties

- Data properties
    - [[Configurable]]: Indicates if the property may be redefined by removing the property via delete, changing the property’s attributes, or changing the property into an accessor property.
    - [[Enumerable]]: Indicates if the property will be returned in a `for-in` loop. 
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

## 继承

### Prototype Chaining

继承的基本原理是：把一个构造函数的prototype指向另一个对象，那么这个构造函数同时也继承了另一个对象构造函数上的prototype属性。

**坏处：prototype中的属性和方法会被所有实例**

注意`instanceof`(**为什么？**)：

```
// subType继承自SuperType，SuperType继承自Object, instance为SubType的实例
alert(instance instanceof Object); //true
alert(instance instanceof SuperType); //true
alert(instance instanceof SubType); //true

1 instanceof Object // false
1 instanceof Number // false

"1" instanceof Object // false
[] instanceof Object //false
```

### Constructor Stealing

在子类中召唤父类的构造函数

**坏处：因为调用构造函数，而非new一个。那么方法也必须定义在构造函数内部，prototype上的属性不能共用。**

```
function SuperType(name){
    this.name = name;
}

function SubType(){ 
    //inherit from SuperType passing in an argument
    SuperType.call(this, “Nicholas”);

    //instance property
    this.age = 29;
}

var instance = new SubType();
alert(instance.name); //”Nicholas”;
alert(instance.age); //29
```

### Constructor Stealing + Prototype Chaining

基本上没有什么坏处，但是下面有更好的办法

```
function SuperType(name){
    this.name = name;
    this.colors = [“red”, “blue”, “green”];
}

SuperType.prototype.sayName = function(){
    alert(this.name);
};

function SubType(name, age){ 
    //inherit properties
    SuperType.call(this, name);
    this.age = age;
}

//inherit methods
SubType.prototype = new SuperType();
SubType.prototype.sayAge = function(){
    alert(this.age);
};
```

### Prototypal Inheritance

```
// Object.create:

function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}

var person = {
    name: “Nicholas”,
    friends: [“Shelby”, “Court”, “Van”]
};

var anotherPerson = object(person);
```

### 终极方案：

```
function inheritPrototype(subType, superType){
    var prototype = object(superType.prototype); //create object
    prototype.constructor = subType; //augment object
    subType.prototype = prototype; //assign object
}

function SuperType(name){
    this.name = name;
    this.colors = [“red”, “blue”, “green”];
}

SuperType.prototype.sayName = function(){
    alert(this.name);
};

function SubType(name, age){ 
    SuperType.call(this, name);
    this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function(){
    alert(this.age);
};
```