## Vue.js

### 知识体系：

* Vue 简介
* Vue 基础语法
* 组件化开发
* Vue CLI 详解
* Vue-router 详解
* Vuex 详解
* 网络封装
* 项目实战
* 项目部署
* Vue.js 原理

### Vue 简介

Vue 是一套用于构建用户界面的渐进式框架。那么如何理解“渐进式”呢？

* 渐进式意味着可以将 Vue 作为应用的一部分嵌入其中，以带来更丰富的交互体验；
* 如果希望将更多的业务逻辑使用 Vue 实现，那么可以将 Vue 的核心库以及其生态系统(Core + Vue-router + Vuex)集成到项目中以满足各种各样的需求。

### Vue 特点

* (1)解耦视图和数据
* (2)可复用的组件
* (3)前端路由技术
* (4)状态管理
* (5)虚拟 DOM

### Vue 安装

利用 NPM 安装

【注意】**ES6中使用 let 关键字定义变量，使用 const 关键字定义常量**


### Vue 中的 MVVM

* M层： Model层,即数据层。具体代码中指的是 Vue 实例中 data 属性所对应的 Value值。可以是本地数据，也可以是请求自服务器的数据。
* V层： View层，即视图层。在前端开发中，通常是指 DOM 层。其主要作用就是给用户展示各种信息。
* VM层： VueModel层，即视图模型层。具体代码中指的是我们创建的 Vue 实例对象。它是 View层和 Model 层之间的调度者。VueModel 层的主要作用是一方面它实现了 Data Binding，也就是数据绑定，将 Model 的改变实时地反映到View中。另一方面它实现了 DOM listener，也就是 DOM 监听，当 DOM 发生一些事件（比如点击、滚动、touch等）时，可以监听到，并在需要的情况下改变对应的 Data。


### 创建 Vue实例传入的 optios

* (1)el:

  ✅类型： **string | HTMLElement**
  
  ✅作用：**挂载将要管理的 DOM。即决定之后 Vue 实例会管理哪一个 DOM**。
  
* (2)data:

  ✅类型：**Object | Function(组件当中data必须是一个函数)**
  
  ✅作用：**Vue实例对应的数据对象**。
  
* methods:

  ✅类型：**{[key:string]:Function}**
  
  ✅作用：**定义属于Vue的一些方法，以供其他地方调用，也可以在指令中使用**


### Vue 的生命周期

* beforeCreate 函数
* created 函数
* mounted 函数





