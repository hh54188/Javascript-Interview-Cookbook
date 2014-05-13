## Content

- Negative Margins
- Collapsing Margins

## Negative Margins

### 如果负外边距的容器**没有浮动**

![margin](./images/margin-motion.gif)

分为两种情况

- 如果你把`margin-top/margin-left`设置为负的，则把该元素往top/left方向上拉

- 如果你把`margin-right/margin-bottom`设置为负的，则把该元素right/bottom方向相邻方向的元素往该元素方向上拉

**永远都往左上角方向移动**

### 如果负外边距容器是**有浮动的**

也分为两种情况

- 如果**负边距方向与浮动方向一致**，则该元素被拉向浮动方向

- 如果**负边距方向与浮动方向相反**，则相邻元素会被拉向该元素

## Collapsing Margins

以下元素不会发生外边距折叠

- floated elements
- absolutely positioned elements
- **inline-block elements**
- elements with overflow set to anything other than **visible**(`overflow:visiable`为默认值) (They do not collapse margins with their children.)
- cleared elements (They do not collapse their top margins with their parent block’s bottom margin.)
- the root element

外边距折叠只会发生在垂直方向。分为两种情况

分为下列三种情况：

- 相邻元素的折叠：当两个或多个元素垂直排列时（标准流中），相邻的两个外边距会重合为一个外边距。
    - 这个重合外边距的值为那两个外边距的较大值；
    - 如果为一正一负的话，则为两个的和值；
    - 如果两个都是负外边距的话，最终值为负的更厉害的那个(http://jsfiddle.net/L2qYY/)。

- 子元素与父元素之间的折叠：当父元素与子元素赤裸裸的接触时（都没有内边距，边框；外边距的直接接触），那么父元素的外边距会与子元素的外边距产生折叠；**思考：如何阻止这种折叠？**
    - 添加内边距或者边框
    - 使用BFC

- 自己的上边距与下边距折叠：这个就有点奇葩了，当你有一个容器，设置了上下外边距，但是content area为空，那么自己的上外边距和下外边距会进行折叠！如果此时两个外边距正好一正一负，并且刚好抵消，那么你就看不到这个盒子了！
