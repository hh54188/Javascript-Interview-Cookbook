# 使用width和height的注意事项

## 提问

markup结构如下：

```
<body>
	<h1>Hello World</h1>
</body>
```

CSS样式：

```
h1 {
	height: 80%;
}
```

问此时`<h1>`的高度是否真的只占用`<body>`的80%？

## 答案

先说结论：

**错，此时`h1`高度仍然与`body`的高度相等**

解释：

[Percentage Height HTML 5/CSS](http://stackoverflow.com/questions/1622027/percentage-height-html-5-css)
[CSS – why doesn’t percentage height work?](http://stackoverflow.com/questions/5657964/css-why-doesn-t-percentage-height-work)

当你给一个块级元素（当然只能是块级元素，行内元素无法设置高度）设置高度时，它的父容器必须要指定高度（百分比，像素值都行）

