# AJAX

## FAQ

- Object与JSON的区别是什么？
- 请写出创建兼容XHR对象的方法?
- 举出跨域请求的解决办法？并说明原理和优劣


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

ECMAScript 5 formalized JSON parsing under a native global called JSON. This object is supported in **Internet Explorer 8+**, Firefox 3.5+, Safari 4+, Chrome, and Opera 10.5+.

## Ajax

### 如何创建兼容的XHR对象

更完美的解决办法，可以参考 http://www.quirksmode.org/js/xmlhttp.html

```
// IE6/IE5
if (window.ActiveXObject) {

    return new window.ActiveXObject("Microsoft.XMLHttp");
    // new ActiveXObject("Msxml2.XMLHTTP")
    // new ActiveXObject("Msxml3.XMLHTTP")

} else {
    return new window.XMLHttpRequest();
}
```

### 如何使用

- xhr.open(“get”, “example.txt”, false); // method, url(same origin domain), async
- xhr.send(null);

```
var xhr = createXHR(); 
xhr.onreadystatechange = function(){
    // 注意这里没有用this，而是用的是xhr
    if (xhr.readyState == 4){
    // 4 — Complete. All of the response data has been retrieved and is available.
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
                alert(xhr.responseText);
        } else {
            alert(“Request was unsuccessful: “ + xhr.status);
        }
    }   
};

xhr.open(“get”, “example.txt”, true);
// 注意这里的url需要encodeURIComponent
**xhr.setRequestHeader(“MyHeader”, “MyValue”);** 
// Accept Accept-Charset Accept-Encoding Accept-Language Connection Cookie Host Referer User-Agent
xhr.send(null);
```

### 请说出一些常见的跨域请求方法，包括原理和优劣

- Image Ping

- **JSONP**: http://www.cnblogs.com/chopper/archive/2012/03/24/2403945.html

原理：创建一个回调函数，然后在远程服务上调用这个函数并且将JSON 数据形式作为参数传递，完成回调。

- Comet(Server push): long polling / streaming