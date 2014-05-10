## 负外边距

http://www.smashingmagazine.com/2009/07/27/the-definitive-guide-to-using-negative-margins/

- 如果你把`margin-top/margin-left`设置为负的，则把该元素往top/left方向上拉
- 如果你把`margin-right/margin-bottom`设置为负的，则把该元素right/bottom方向相邻方向的元素往该元素方向上拉

## 重点

- 在`Side-Main-Side`布局中，对**侧边栏**应用`margin-left: -100%;`非常重要

- 一定要给中间元素添加一个父容器：
```
<div class="wrap">
    <div class="content"></div>
</div>
```
所有的侧边栏付外边距都是相对于外面的`wrap`

- 适当的时候也要应用`float:right`，详情可以参考`Side-Side-Main`中的`fix-fix-liquid`布局