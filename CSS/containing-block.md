# 包含块 Containing Block

这个概念在W3C文档中写的非常易懂，就直接照抄了：

http://www.w3.org/TR/CSS2/visudet.html

## Definition of "containing block"

>10.1 Definition of "containing block"

>The position and size of an element's box(es) are sometimes calculated relative to a certain rectangle, called the containing block of the element. The containing block of an element is defined as follows:

>1. The containing block in which the root element lives is a rectangle called the initial containing block. For continuous media, it has the dimensions of the viewport and is anchored at the canvas origin; it is the page area for paged media. The 'direction' property of the initial containing block is the same as for the root element.
2. For other elements, if the element's position is 'relative' or 'static', the containing block is formed by the content edge of the nearest block container ancestor box.
3. If the element has 'position: fixed', the containing block is established by the viewport in the case of continuous media or the page area in the case of paged media.
4. If the element has 'position: absolute', the containing block is established by the nearest ancestor with a 'position' of 'absolute', 'relative' or 'fixed', in the following way:
    1. In the case that the ancestor is an inline element, the containing block is the bounding box around the padding boxes of the first and the last inline boxes generated for that element. In CSS 2.1, if the inline element is split across multiple lines, the containing block is undefined.
    2. Otherwise, the containing block is formed by the padding edge of the ancestor.

>If there is no such ancestor, the containing block is the initial containing block.


一个元素的位置和尺寸通常相对于一个矩形计算的，这个矩形就被称为这个元素的**包含块(Containing Block)**，包含块的定义如下：

1. 根元素所在的包含块被称为初始包含块(initial containing block)，可以看做浏览器的视口(viewport)，或者页面区域(page area)
2. 如果元素的position属性是`relative`或者`static`，那么包含块是:距离最近的块状祖先元素(nearest block container ancestor box)的**内容边缘(content edge)**
3. 如果元素的position属性是`fixed`，包含块为浏览器视口(viewport)
4. 如果元素的position属性是`absolute`，那么包含块所在的元素是:带有`position`属性(除`relative`)的最近元素，还要分两种情况：
    - 如果这个祖先元素是行内元素，那么包含块是行内元素夸行产生的`bounding box`的padding box？
    - 如果是块级元素，包含块是祖先的内边距边缘