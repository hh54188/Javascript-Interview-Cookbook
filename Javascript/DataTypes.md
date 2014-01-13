# Data Types

## Contents

- [typeof && instanceof](#typeof--instanceof)
- [Primitive](#primitive)
- [Reference Types](#reference-types)
- [Argument passing](#argument-passing)

## typeof && instanceof

>Although typeofworks well for primitive values, it’s of little use for reference values

>result = variable instanceof constructor

>The instanceof operator returns true if the variable is an instance of the given reference type 

**Something wrong with `typeof`**:

- `typeof new Number // object`
- `typeof null // object`
- `typeof NaN // number`

**The difference between `String value(primitive string)` and [String object](http://es5.github.io/#x4.3.16)**

- A String object is created by using the String constructor in a new expression, supplying a String value as an argument
- A String object can be coerced to a String value by calling the String constructor as a function:
    - `typeof new String("a") // object`
    - `typeof String(new String("a")) // string`

**Object.prototype.toString.call()**

- `Object.prototype.toString.call(n) // [object number]`

## Primitive

- Boolean
- Number
- String
- Undefined
    - When a variable is 
declared using varbut not initialized, it is assigned the value of undefined
    - When compared with the 
literal value of undefined, the two are equal
    - a variable containing the value of undefinedis different from a variable that hasn’t been 
defined at all
    - The typeofoperator returns “undefined”when called on an uninitialized variable, but it also 
returns “undefined”
- Null
    - **A null value is an empty object pointer(empty object reference), which is why `typeof`returns “object” when it’s passed a `null` value** `var car = null; alert(typeof car); //”object”`
    - you should never explicitly set the value of a variable to undefined
    - Any time an object is expected but is not available, nullshould be 
used in its place

## Reference Types

- Object
- Array
- Date
- RegExp
- Function
- Primitive Wrapper Types(String Object/Number Object/Boolean Object):

    Professional Javascript for Web Developers, 3ed Edition: Chapter 5: Reference Types: Primitive Wrapper Types:

    ```
    var s1 = “some text”;
    var s2 = s1.substring(2);
    ```

        In this code, s1is a variable containing a string, which is a primitive value. On the next line, the 
    substring()method is called on s1and stored in s2. Primitive values aren’t objects, so logically 
    they shouldn’t have methods, though this still works as you would expect. In truth, there is a lot 
    going on behind the scenes to allow this seamless operation. When s1is accessed in the second line, 
    it is being accessed in read mode, which is to say that its value is being read from memory. Any time 
    a string value is accessed in read mode, the following three steps occur:

    1. Create an instance of the Stringtype.
    2. Call the specified method on the instance.
    3. Destroy the instance.

    You can think of these three steps as they’re used in the following three lines of ECMAScript code:
    ```
    var s1 = new String(“some text”);
    var s2 = s1.substring(2);
    s1 = null;
    ```
    This behavior allows the primitive string value to act like an object.



## Argument Passing

All function arguments in ECMAScript are passed by value. This means that the value outside of 
the function is copied into an argument on the inside of the function the same way a value is copied 
from one variable to another