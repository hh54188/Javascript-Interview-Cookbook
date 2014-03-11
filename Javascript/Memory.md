# 内存相关

## FAQ

- 什么是内存泄露？
- 请举出内存泄露的例子

### 内存泄露模式

http://javascript.info/tutorial/memory-leaks

```
function setHandler() {
    var elem = document.getElementById('id')
    elem.onclick = function() {
        // ...
    }
}
```
![lead](./images/memory_leak_01.png)

为什么会形成这样的作用域链，请参考函数部分。

如何解决这个问题呢：

![lead](./images/memory_leak_02.png)

同样XMLHttpRequest也有同样的问题：

```
var xhr = new XMLHttpRequest() // or ActiveX in older IE
xhr.open('GET', '/server.url', true)
xhr.onreadystatechange = function() {
  if(xhr.readyState == 4 && xhr.status == 200) {           
    // ...
  }
}
 
xhr.send(null)
```

![xhr](./images/xhr.png)

