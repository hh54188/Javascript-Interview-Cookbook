# Load Javascript Without Blocking

##目录

- 载入脚本的方式
    - 传统
    - defer
    - async
    - dom element
    - xhr
- 实战情况

## 传统方式

传统方式有什么问题：

```
<script type="text/javascript" src="//a.js"></script>
<script type="text/javascript" src="//b.js"></script>
```

浏览器当遇到这两个标签时，会停止页面的渲染，最糟糕的情况是，逐个下载脚本，并且逐个执行

一段阻塞的脚本意味着直到下列三个阶段完成页面才能继续渲染：

1. Completely downloaded
2. Parsed
3. Executed

而其中又属第一条请求脚本最耗费时间，每发出一个请求意味着建立连接，dns查找，等待响应。浏览器阻塞的时间几乎等于脚本响应的时间；

现代浏览器能做到同时下载两个脚本（IE 8、Firefox 3.5、Safari 4 和 Chrome 2 开始都允许并行下载 JavaScript 文件），或者继续在后台下载页面上请求的其他资源。但页面的渲染仍然是被阻塞的。

所以大部分人推荐把脚本放在页面底部，在body标签闭合之前。

## defer

微软从IE4起就支持defer属性了

当浏览器遇见带有defer属性的script标签时，开始下载脚本，并且不会阻塞浏览器进程。即使下载完成之后也不会立即执行，直到DOM加载完成之后(`DOMContentloaded`事件触发时)，或者在onload事件中（但是比onload前一些）

defer能够保证脚本按顺序执行

defer对缺少src的脚本和动态添加的脚本无效

但是不同浏览器对defer的支持却非常糟糕：[Mozilla wrote a great piece on the problem](https://hacks.mozilla.org/2009/06/defer/)

## async

async属性与defer属性类似，能够让浏览器同时下载多个脚本而不阻塞浏览器。

不同的是对动态添加的脚本同样有效。并且一旦下载完成后会立即执行，对执行的顺序没有**保证**:

>When set using HTML, the behavior is very straightforward as discussed earlier. When set on a dynamic script node, the behavior has a subtle distinction. Firefox and Opera preserve the order of execution for external JavaScript files, so you are guaranteed that scripts will execute in order when two dynamic script nodes are added one after the other......Internet Explorer, Safari, and Chrome do not preserve the order of execution, as scripts are executed as soon as they are retrieved regardless of the order in which they were inserted. In these browsers, setting async on script nodes has no effect (but also doesn’t hurt anything).

## DOM Element

**Scripts that are dynamically created and added to the document are async by default.**

```
[
    '//other-domain.com/1.js',
    '2.js'
].forEach(function(src) {
    var script = document.createElement('script');
    script.src = src;
    script.async = false;
    document.head.appendChild(script);
});
```

通过将async属性设为false，保证脚本能够被添加入一个执行队列中。保证动态创建的脚本按顺序执行

又或者使用[Nicholas C. Zakas](http://www.nczonline.net/blog/2009/06/23/loading-javascript-without-blocking/)的方法：

```
function loadScript(url, callback){

    var script = document.createElement("script")
    script.type = "text/javascript";

    if (script.readyState){  //IE
        script.onreadystatechange = function(){
            if (script.readyState == "loaded" || script.readyState == "complete"){
                script.onreadystatechange = null;
                callback();
            }
        };
    } else {  //Others
        script.onload = function(){
            callback();
        };
    }

    // IE在创建script标签时，是在赋值src是就开始下载
    script.src = url;

    // 大部分浏览器是在节点插入文档时才开始下载脚本
    // 这与动态创建<img>标签不同，创建img时一旦赋值src，图片就已开始下载
    // 但是IE创建script标签时，是在赋值src是就开始下载
    document.body.appendChild(script);
}
```

## link rel="prefetch"

### [What is link prefetching?](https://developer.mozilla.org/en/docs/Link_prefetching_FAQ#What_is_link_prefetching.3F)

Link prefetching is a browser mechanism, 

which utilizes browser idle time to download or prefetch documents that the user might visit in the near future. 

A web page provides a set of prefetching hints to the browser, and after the browser is finished loading the page, it begins silently prefetching specified documents and stores them in its cache. 

When the user visits one of the prefetched documents, it can be served up quickly out of the browser's cache.

### [What is LINK rel=subresource?](http://www.chromium.org/spdy/link-headers-and-server-hint/link-rel-subresource)

"LINK rel=subresource" provides a new link relation type with different semantics from LINK rel=prefetch.   

While rel=prefetch provides a **low-priority** download of resources to be used on subsequent pages, rel=subresource enables early loading of resources within the current page.  **Because the resource is intended for use within the current page**, it must be loaded at high priority in order to be useful.

## XMLHttpRequest(XHR)

```
var xhr = new XMLHttpRequest();
xhr.open("get", "script1.js", true);
xhr.onreadystatechange = function(){
    if (xhr.readyState == 4){
        if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304){
            var script = document.createElement ("script");
            script.type = "text/javascript";
            script.text = xhr.responseText;
            document.body.appendChild(script);
        }
    }
};
xhr.send(null);
```

这种方法的主要优点是，您可以下载不立即执行的 JavaScript 代码。由于代码返回在`<scrip\`标签之外（换句话说不受`<script\`标签约束），它下载后不会自动执行，这使得您可以推迟执行，直到一切都准备好了。另一个优点是，同样的代码在所有现代浏览器中都不会引发异常。
此方法最主要的限制是：JavaScript文件必须与页面放置在同一个域内，不能从CDN下载，所以大型网页通常不采用 XHR 脚本注入技术。

## 实战情况

我们以[sea.js](https://github.com/seajs/seajs), [require.js](https://github.com/jrburke/requirejs), [lab.js](https://github.com/getify/LABjs)为例

从前面可以得知，**脚本的载入和执行是两件完全不同的事情**，普遍情况是，脚本的载入可以无序，但是执行必须是有序。按照labjs作者的[话说](http://blog.getify.com/labjs-script-loading-the-way-it-should-be/)，最理想的情况应该是

>Understand that what’s at question is how to load 2 or more scripts, in parallel, but enforce the execution order, if there is a dependency between them. **And not just that, but to be able to load any scripts at any time, from anywhere, without modification of the script contents.**

虽然之前有那么多的方法可以供选择（更多可以参考Steve Souder的[Loading Scripts Without Blocking](http://www.stevesouders.com/blog/2009/04/27/loading-scripts-without-blocking/)）。但至少这三个库而言，使用的解决方案都是dom element：

- sea.js:

```
function request(url, callback, charset) {
  var isCSS = IS_CSS_RE.test(url)
  var node = doc.createElement(isCSS ? "link" : "script")

  if (charset) {
    var cs = isFunction(charset) ? charset(url) : charset
    if (cs) {
      node.charset = cs
    }
  }

  addOnload(node, callback, isCSS, url)

  if (isCSS) {
    node.rel = "stylesheet"
    node.href = url
  }
  else {
    node.async = true
    node.src = url
  }
  ......

```

- require.js:

```
/**
 * Creates the node for the load command. Only used in browser envs.
 */
req.createNode = function (config, moduleName, url) {
    var node = config.xhtml ?
            document.createElementNS('http://www.w3.org/1999/xhtml', 'html:script') :
            document.createElement('script');
    node.type = config.scriptType || 'text/javascript';
    node.charset = 'utf-8';
    node.async = true;
    return node;
};
// 但是它还判断了如果是webWorker的情况
else if (isWebWorker) {
            
```

- lab.js:

可以参考labjs作者写的一篇解析文章：

[LABjs: script loading the way it “should” be](http://blog.getify.com/labjs-script-loading-the-way-it-should-be/);

它们同时使用dom element的好处是，保证了脚本的下载不会阻塞浏览器进程。

但是执行顺序呢？执行是否会阻塞浏览器进程？

答案是肯定的，从timeline中的evaluate script中就能看出来

setPlay, ugcIndex的evaluate script耗时比较长的，建议放在后面

qa，ver的evaluate script耗时比较短，可以放在前面





