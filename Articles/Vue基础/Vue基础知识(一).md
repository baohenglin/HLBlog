## 要点

* Vue 的基本语法和概念
* 打包工具 Webpack、Gulp

## 为什么要使用框架？

使用前端框架，是为了**提高开发效率**。

提高开发效率的发展历程：原生JS -> Jquery 之类的类库 -> 前端模板引擎 -> Angular.js/Vue.js/React.js。

* 原生JS：手写的原生JS代码存在浏览器兼容性问题。
* JQuery 之类的类库：虽然解决了各大浏览器兼容性问题，但是需要频繁操作 DOM 元素。
* 前端模板引擎：解决了JQuery类库“频繁操作 DOM”的问题。只需要调用前端模板引擎提供的方法，就可以渲染 DOM 元素。但是存在“所有 DOM 元素都被重新渲染一遍”的性能问题。
* Angular.js/Vue.js/React.js：这些前端框架解决了“前端模板引擎存在的‘所有 DOM 元素都被重新渲染一遍’的性能问题”，能够帮助我们减少不必要的 DOM 操作，提高渲染效率。同时还具备双向数据绑定功能（通过框架提供的指令，开发者只需要关注数据的业务逻辑，不必再关心 DOM 元素是如何渲染的了。）


## Vue.js简介

Vue.js 与 React.js、Angular.js 合称为前端三大主流框架。Vue.js是一套构建用户界面的渐进式框架。与其他重量级框架不同的是，Vue采用自底向上增量开发的设计。Vue的核心库只关注**视图层**，并且非常容易学习，非常容易与其他库或已有项目整合。另一方面，Vue完全有能力驱动采用单文件组件和Vue生态系统支持的库开发的复杂单页应用。

Vue.js 是目前最火的前端框架，React.js 是最流行的前端框架（React除了可以开发网站，还可以开发手机 App，Vue语法也可以用于进行手机App的开发，但是需要借助于Weex。）

Vue.js的目标是**通过尽可能简单的 API 实现响应式的数据绑定和组合的视图组件。通过使用 Vue ，开发者不再直接操作 DOM 元素，解放了开发者的双手，让程序员可以更多的关注业务逻辑。

[Vue的Github地址](https://github.com/vuejs)

## Vue.js特点

* 易用：已经学会了 HTML、CSS、JavaScript，那么现在就可以利用Vue开始构建应用。
* 灵活：简单小巧的核心，渐进式技术栈，足以应付任何规模的应用。
* 性能高：20kb min+gzip运行大小，超快虚拟 DOM、最省心的优化。

## Vue.js 2.0安装

在兼容性方面，Vue.js不支持 IE8及其以下版本，因为 Vue.js使用了 IE8不能模拟的 ECMAScript5特性。Vue.js支持所有兼容 ECMAScript 5的浏览器。

**npm**

在用Vue.js构建大型应用时推荐使用NPM安装，npm 能很好地和诸如 Wevpack 或 Browserify 模块打包器配合使用。Vue.js也提供配套工具来开发单文件组件。

```
//最新稳定版
npm install vue
```

## Vue模板语法

```
v-on:click
```

v-on:click可简写为@click。v-on指令用于监听 DOM 事件。

```
v-bind
```
v-bind可简写为“:”。v-bind指令用于响应式地更新 HTML特性。

## v-model和 :model的区别

v-model是vue.js中内置的双向数据绑定指令，用于表单控件以外的标签是不起作用的(即只对表单控件标签的数据双向绑定有效)。

:model相当于v-bind:model的缩写，v-bind动态绑定指令，默认情况下标签自带属性的值是固定的，可以使用v-bind:'需要绑定的值'='表达式'，（如：  :disabled等）。
