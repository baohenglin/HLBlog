## 要点

* Vue 的基本语法和概念
* 打包工具 Webpack、Gulp

## 为什么要使用前端框架？

使用前端框架，是为了**提高开发效率**。

提高开发效率的发展历程：原生JS -> Jquery 之类的类库 -> 前端模板引擎 -> Angular.js/Vue.js/React.js。

* 原生JS：手写的原生JS代码存在浏览器兼容性问题。
* JQuery 之类的类库：虽然解决了各大浏览器兼容性问题，但是需要频繁操作 DOM 元素。
* 前端模板引擎：解决了JQuery类库“频繁操作 DOM”的问题。只需要调用前端模板引擎提供的方法，就可以渲染 DOM 元素。但是存在“所有 DOM 元素都被重新渲染一遍”的性能问题。
* Angular.js/Vue.js/React.js：这些前端框架解决了“前端模板引擎存在的‘所有 DOM 元素都被重新渲染一遍’的性能问题”，能够帮助我们减少不必要的 DOM 操作，提高渲染效率。同时还具备双向数据绑定功能（通过框架提供的指令，开发者只需要关注数据的业务逻辑，不必再关心 DOM 元素是如何渲染的了。）

## 框架和库的区别

* 框架：是一套完整的技术解决方案；框架对项目的侵入性较大，项目如果需要更换框架，则需要重新架构整个项目。
* 库：库也称为插件。提供某一个小功能，对项目的侵入性较小，如果某个库无法完成某些需求，可以很容易切换到其他库实现需求。比如从 JQuery 切换到 Zepto，从EJS 切换到 art-template。

## Vue.js简介

Vue.js 与 React.js、Angular.js 合称为前端三大主流框架。Vue.js是一套构建用户界面的渐进式框架。与其他重量级框架不同的是，Vue采用自底向上增量开发的设计。Vue的核心库只关注**视图层**，并且非常容易学习，非常容易与其他库或已有项目整合。另一方面，Vue完全有能力驱动采用单文件组件和Vue生态系统支持的库开发的复杂单页应用。

Vue.js 是目前最火的前端框架，React.js 是最流行的前端框架（React除了可以开发网站，还可以开发手机 App，Vue语法也可以用于进行手机App的开发，但是需要借助于Weex。）

Vue.js的目标是**通过尽可能简单的 API 实现响应式的数据绑定和组合的视图组件**。通过使用 Vue ，开发者不再直接操作 DOM 元素，解放了开发者的双手，让程序员可以更多的关注业务逻辑。

[Vue的Github地址](https://github.com/vuejs)

## Vue.js特点

* 易用：已经学会了 HTML、CSS、JavaScript，那么现在就可以利用Vue开始构建应用。
* 灵活：简单小巧的核心，渐进式技术栈，足以应付任何规模的应用。
* 性能高：20kb min+gzip运行大小，超快虚拟 DOM、最省心的优化。

## Vue.js 2.安装

在兼容性方面，Vue.js不支持 IE8及其以下版本，因为 Vue.js使用了 IE8不能模拟的 ECMAScript5特性。Vue.js支持所有兼容 ECMAScript 5的浏览器。

**npm**

在用Vue.js构建大型应用时推荐使用NPM安装，npm 能很好地和诸如 Wevpack 或 Browserify 模块打包器配合使用。Vue.js也提供配套工具来开发单文件组件。

```
//最新稳定版
npm install vue
```

## Vue模板语法

### 插值表达式 {{}}

```
// 插值表达式 "{{ }}"
<p>{{ msg }}</p>
```

### v-cloak 

v-cloak 可以**解决插值表达式在慢网速时闪烁的问题**。

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

```
v-on:click
```

### v-text

```
<h4 v-text="msg"></h4>
```

**v-text与插值表达式的区别**： (1)v-text默认不存在像插值表达式那样的闪烁问题；(2)v-text会覆盖元素中原本的内容，但是插值表达式只会替换自己的这个占位符，不会把整个元素的内容清空。

### v-html 解析HTML文本

```
<div v-html="<h1>最大的标题</h1>"></div>
```

### v-bind: 指令

"v-bind:"是 Vue 中，提供的用于绑定属性的指令。也就是说v-bind指令可以响应式地更新 HTML 属性。

```
v-bind:title="myTitle + '123'"
//等同于 :title="myTitle + '123'"
```
**v-bind可简写为“:”**。

### v-on: 指令用于绑定某些事件(click事件、mouseover事件)

v-on指令用于监听 DOM 事件。

```
<input type="button" value="按钮" v-on:click="show">
...
// ES5写法
methods: {
    show: function() {
        alert('Hello');
    }
}
// ES6写法（推荐此写法）
methods: {
    show() {
        alert('Hello');
    }
}
```

"v-on:click='show'"可简写为"@click='show'"。

### 跑马灯示例

```
<script>
//【注意】在Vue实例对象中，如果想要获取 data 中的数据或者想要调用 methods 中的方法，必须通过 this.数据属性名/this.方法名 来进行访问。这里的this就表示我们 new 的 Vue 实例对象。
var vm = new Vue({
    el: '#app',
    data: {
        msg:'武汉加油！中国加油！！！',
        intervalId: null // 在 data中定义定时器id
    },
    methods: {
        run() {
            //判断是否已经开启了定时器
            if(this.intervalId != null) return;
           
            // ES6写法（推荐）：箭头函数来解决 “this 指向”的问题。箭头函数的形式可以确保箭头函数内部的 this 永远指向箭头函数外部的 this。
            // 不推荐使用 var _this = this; 这种方式。
            this.intervalId = setInterval( () => {
                // 获取字符串第一个字符
                var start = _this.msg.substring(0, 1)
                // 获取字符串非第一个字符的子串
                var end = _this.msg.substring(1)
                // 重新拼接得到新的字符串，并赋值给 _this.msg
                _this.msg = end + start
                
            }, 500)
        },
        stop() {
            clearInterval(this.intervalId); // 清除定时器
            // 清除了定时器之后，需要把 this.intervalId 置为 null。
            this.intervalId = null;
        }
    }
})
</script>
```

### 事件修饰符

* .stop 阻止冒泡
* .prevent 阻止默认事件
* .capture 添加事件监听器时使用事件捕获模式
* .self 只当事件在该元素本身（比如不是子元素）触发时触发回调
* .once 事件只触发一次

```
<div class="inner" @click="div1Handler">
  <!--使用 .stop 阻止冒泡-->
  <input type="button" value="点击按钮" @click.stop="btnHandler"></input>
  
  <!--使用 .prevent 阻止默认行为-->
  <a href="http://www.baidu.com" @click.prevent="linkClick">跳到百度</a>
  
  <!--使用 .capture 实现捕获触发事件的机制：先触发 div 的点击事件再触发 input 的点击事件-->
  <input type="button" value="点击按钮" @click.capture="btnHandler"></input>
</div>
```

```
// 使用 .self 实现只有点击当前元素时，才会触发事件处理函数。
// 使用 .self 事件修饰符：只有点击 div 时才会触发其点击事件。
<div class="inner" @click="div1Handler">
  <input type="button" value="点击按钮" @click="btnHandler"></input>
</div>
```

```
// 使用 .once 只触发一次事件处理函数。
<div class="inner" @click="div1Handler">
  <a href="http://www.baidu.com" @click.prevent.once="linkClick">跳到百度</a>
</div>
```

** .self 和 .stop 的区别：**.self只阻止当前元素的事件冒泡，不能阻止其他元素的事件的冒泡；.stop 阻止所有元素事件冒泡。

## v-model和 :model的区别

v-model是vue.js中内置的双向数据绑定指令，用于表单控件以外的标签是不起作用的(即只对表单控件标签的数据双向绑定有效)。

:model相当于v-bind:model的缩写，v-bind动态绑定指令，默认情况下标签自带属性的值是固定的，可以使用v-bind:'需要绑定的值'='表达式'，（如：  :disabled等）。
