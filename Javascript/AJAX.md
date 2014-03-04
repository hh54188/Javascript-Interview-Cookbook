# AJAX

## FAQ

- Object与JSON的区别是什么？


## JSON

### Object与JSON的语法区别

- JSON值只允许三种数据类型(注意，不允许function，Date等数据类型)：
    - Simple Value: Strings, numbers, Booleans, and null
    - Objects
    - Arrays
- 不存在声明，也不应该用分号隔开
- 属性名称应该由双括号（但括号不行）括起来(object property names in JSON must always be double-quoted.):

```
// 应该是
{
    "name": "lee"
}
// 而不是
var obj = {
    name: "lee"
};
```

### JSON Object

ECMAScript 5 formalized JSON parsing under a native global called JSON. This object is supported in Internet Explorer 8+, Firefox 3.5+, Safari 4+, Chrome, and Opera 10.5+.