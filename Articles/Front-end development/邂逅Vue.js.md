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


### Vue 的生命周期（钩子函数）

* beforeCreate 函数：组件实例刚被创建，组件属性计算之前，如 data 属性等
* created 函数：组件实例创建完成，属性已绑定，但 DOM 还未生成， $el 属性还不存在。
* beforeMount 函数：模板编译/挂载之前
* mounted 函数：模板编译/挂载之后
* beforeUpdate 函数：组件更新之前
* updated 函数：组件更新之后
* activated 函数：组件被被激活时调用，keep-alive
* deactivated 函数：组件被移除时调用
* beforeDestory 函数：组件销毁前调用
* destoryed 函数：组件销毁后调用


### Vue 基本语法

#### 1. Mustache 语法（双大括号语法/插值操作）

【注意】标签之间的内容显示可以使用 Mustache 语法，但是标签内部不能使用 Mustache 语法。

```
<h2>{{message}}</h2>
<h2>{{message}}，中国加油！</h2>
<h2>{{firstName + '' + lastName}}</h2>
<h2>{{firstName + '' + lastName}}</h2>
<h2>{{firstName}} {{lastName}}</h2>
<h2>{{counter * 2}}</h2>
```

#### 2. v-once 指令

v-once指令：**该指令表示元素和组件只渲染一次，不会随着数据的改变而改变**。该指令后面不需要跟任何表达式。

应用场景：在某些情况下，我们可能不希望界面响应式地随数据随意改变，此时可以使用 v-once 指令。

```
<h2 v-once>{{message}}</h2>
```

#### 3. v-html 指令

```
<h2 v-html="<a href='http://www.baidu.com'>百度一下</a>"></h2>
```

#### 4. v-text 指令

v-text指令作用与 Mustache 比较相似，都是用于将数据显示在界面中。但是 v-text 与 Mustache相比不够灵活，会将原有的内容替换掉。

```
<h2 v-text="message"></h2>
```

#### 5. v-pre 指令

```
<h2>{{message}}</h2>        //会解析 message
<h2 v-pre>{{message}}</h2>  //不会解析 message，原封不动的展示 ‘{{message}}’
```

#### 6. v-cloak 指令

v-cloak 可以**解决插值表达式在慢网速时闪动的问题**。

```
<head>
<style>
  [v-cloak] {
    display:none;
  }
</style>
</head>

<body>
<p v-cloak>{{ msg }}</p>
</body>
```

#### 6. v-bind 指令：

作用：**用于动态绑定属性（比如a元素的href属性；img元素的src属性）**

v-bind缩写(语法糖)：**:**

```
<img v-bind:src="imgURL" alt="">     //等同于 <img :src="imgURL" alt="">
<a v-bind:href="aHref">百度一下</a>   //等同于 <a :href="aHref">百度一下</a>  
v-bind:title="myTitle + '加油'"      //等同于 :title="myTitle + '123'"

<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '中国加油',
      imgURL: 'https://img.png',
      aHref: 'http://www.baidu.com',
      myTitle: '武汉'
    }
  })
</script>
```

**v-bind 动态绑定 class 对象语法**

```
<div>
  // 用法1：直接通过 {} 绑定一个类
  <h2 class="title" v-bind:class="{active: isActive, line: isLine}">{{message}}</h2>
  // 用法2：如果过于复杂，可以放在 getClasses 方法中 或者computed 计算属性中。
  <h2 class="title" v-bind:class="getClasses()">{{message}}</h2>
  <button v-on:click="btnClick">按钮</button>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '中国加油',
      imgURL: 'https://img.png',
      aHref: 'http://www.baidu.com',
      myTitle: '武汉',
      isActive: true,
      isLine: true
    },
    methods: {
      btnClick: function () {
        this.isActive = !this.isActive;
      },
      getClasses: function () {
        return {active: this.isActive, line: this.isLine};
      }
    }
  })
</script>
```

**v-bind 动态绑定 class 数组语法（动态绑定一个数组）**

```
<div>
  // 这种写法用的很少
  <h2 class="title" v-bind:class="[active, line]">{{message}}</h2>
  <button v-on:click="btnClick">按钮</button>
</div>

<script>
  const app = new Vue({
    el: '#app',
    data: {
      active: '中国加油',
      line: '武汉加油'
    }
  })
</script>
```

**v-bind 动态绑定 style 样式（组件化开发中使用）**

我们可以利用 v-bind:style 来绑定一些 CSS 内联样式。在写 CSS 属性名的时候，比如 font-size，我们可以使用驼峰式(fontSize)，也可以使用短横线式，使用短横线式，必须使用单引号括起来('font-size')。

* 对象语法
* 数组语法














