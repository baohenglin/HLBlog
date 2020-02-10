## 要点

* 1. Vue 的基本语法和概念
* 2. 打包工具 Webpack、Gulp

## Vue.js简介

Vue.js是一套构建用户界面的渐进式框架。与其他重量级框架不同的是，Vue采用自底向上增量开发的设计。Vue的核心库只关注视图层，并且非常容易学习，非常容易与其他库或已有项目整合。另一方面，Vue完全有能力驱动采用单文件组件和Vue生态系统支持的库开发的复杂单页应用。

Vue.js的目标是通过尽可能简单的API实现响应的数据绑定和组合的视图组件。

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
