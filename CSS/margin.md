## Content

- Negative Margins
- Collapsing Margins

## Negative Margins

### 如果负外边距的容器**没有浮动**

![margin](./images/margin-motion.gif)

分为两种情况

- 如果你把`margin-top/margin-left`设置为负的，则把该元素往top/left方向上拉

- 如果你把`margin-right/margin-bottom`设置为负的，则把该元素right/bottom方向相邻方向的元素往该元素方向上拉

### 如果负外边距容器是**有浮动的**

也分为两种情况

- 如果**负边距方向与浮动方向一致**，则该元素被拉向浮动方向

- 如果**负边距方向与浮动方向相反**，则相邻元素会被拉向该元素
