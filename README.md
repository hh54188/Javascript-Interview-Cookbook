# 前端面试大全
 
## React & Flux & Vuex

 [React解决了什么问题](#why_need_react)
- **[如何设计一个好的组件？](#design_component)**
- **[组件的Render函数在何时被调用？](#when_render_invoked)**
    - 调用时DOM就一定会被更新吗？
- [组件的生命周期有哪些？](#react_lifecircle)
    - 当某些第三方类库想对DOM初始化，或者进行远程数据加载时，应该在哪个周期中完成？
    - 在哪些声明周期中可以修改组件的state？
- **[不同父节点的组件需要对彼此的状态进行改变时应该实现？](#component_communication)**
    - 如何设计出一个好的Flux架构
    - 如何设计出一个好的React组件
- [如何进行优化？](#component_optimize)
    - 组件中的key属性有什么用？
- [Component 与 Element 与 Instance 的区别](#component_element_diff)
- **[如果你使用过Redux与Vuex的话，聊聊他们的区别与你的心得](#flux_vs_vuex)**
    - Vue.js 的双向绑定是如何实现的？
- [Webpack如何打包输出多个文件？](#about_webpack)
    - webpack打包时如何工作的？
        - 如何解决循环引用的问题
    - 在什么情况下需要打包输出多个文件？
    - loader和plugin的差别
    - 你觉得使用过什么高级技巧吗？
- [（开放问题）React的生态你使用过哪些类库](#webpack_ecology)