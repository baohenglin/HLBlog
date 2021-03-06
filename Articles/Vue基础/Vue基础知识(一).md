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

【注意】**在Vue实例对象中，如果想要获取 data 中的数据或者想要调用 methods 中的方法，必须通过 this.数据属性名/this.方法名 来进行访问。这里的this就表示我们 new 的 Vue 实例对象**。

```
<script>
//【注意】在Vue实例对象中，如果想要获取 data 中的数据或者想要调用 methods 中的方法，必须通过 this.数据属性名/this.方法名 来进行访问。这里的this就表示我们 new 的 Vue 实例对象。
var vm = new Vue({
    // el 指定要控制的区域
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

**【注意】** .self 和 .stop 的区别：.self只阻止当前元素的事件冒泡，不能阻止其他元素的事件的冒泡；.stop 阻止所有元素事件冒泡。

### v-model 指令用于双向数据绑定（v-model 是唯一一个实现双向数据绑定的 Vue 指令。）

v-model 指令用于双向数据绑定（v-model 是唯一一个实现双向数据绑定的 Vue 指令。）；v-bind只能实现数据的单向绑定，从 Model 自动绑定到 View。无法实现数据的双向绑定。

**v-model和 :model的区别**

v-model是vue.js中内置的双向数据绑定指令，用于表单元素以外的标签是不起作用的(即**只对表单元素的数据双向绑定有效**)。表单元素包括 input(radio、text、address、email……)，select，checkbox，textarea等。

:model相当于v-bind:model的缩写，v-bind动态绑定属性指令。默认情况下标签自带属性的值是固定的，可以使用 v-bind:'需要绑定的值'='表达式'，（如：  :disabled等）。

### 在 Vue 中使用样式

#### 1. 使用class样式

* (1)数组

```
<head>
  <script src="./lib/vue-2.4.0.js"></script>
  <style>
      .red {
          color: red;
      }

      .thin {
          font-weight: 200;
      }

      .italic {
          font-style: italic;
      }

      .active {
          letter-spacing: 0.5em; // 设置字符间距
      }
  </style>
</head>

<body>
  <div id="app">
      //<h1 class="red thin">这是一个很大很大的H1，大到你无法想象</h1>
      <!--第一种使用方式：直接传递一个数组。注意：这里的 class 需要使用 v-bind 做数据绑定。 -->
      <h1 :class="['thin', 'italic']">这是一个很大很大的H1，大到你无法想象</h1>
  </div>
  <script>
      var vm = new Vue({
          el: '#app',
          data: {
              flag: true
          },
          methods: {
          
          }
      });
  </script>
</body>
```
* (2)数组中使用三元表达式

```
<h1 :class="['thin', 'italic', flag?'active':'']">这是一个很大很大的H1，大到你无法想象</h1>
```
* (3)数组中嵌套对象

```
// 在数组中使用 对象来代替三元表达式，提高代码的可读性。
<h1 :class="['thin', 'italic', {'active':flag}]">这是一个很大很大的H1，大到你无法想象</h1>
```

* (4)直接使用对象

```
//在为 class 使用 v-bind 绑定对象时，对象的属性是类名，其中对象的属性可带引号，也可以不带引号。属性的 value 是一个标识符。
<h1 :class="{ red: true, thin: true, italic: false, active: false}">这是一个很大很大的H1，大到你无法想象</h1>
```

#### 2. 使用内联样式

* (1)直接在元素上通过 :style 的形式，书写样式对象

```
<h1 :style="{ color: 'red', 'font-weight':200}">这是一个很大很大的H1，大到你无法想象</h1>
```

* (2)将样式对象，定义到 data 中，并直接引用到 :style 中。

```
<h1 :style="styleObj1">这是一个很大很大的H1，大到你无法想象</h1>
<script>
      var vm = new Vue({
          el: '#app',
          data: {
              flag: true,
              styleObj1: { color: 'red', 'font-weight':200}
          },
          methods: {
          
          }
      });
  </script>
```

* (3)在 :style 中通过数组，引用多个 data上的样式对象。

```
<h1 :style=[h1styleObj1, h1styleObj2]>这是一个很大很大的H1，大到你无法想象</h1>
data: {
    h1styleObj1: { color: 'red', 'font-size': '40px', 'font-weight': '200'},
    h1styleObj2: { fontStyle: 'italic'}
}
```

### v-for 指令

* (1)循环普通数组


```
<div>
   <p v-for="(item, i) in list">索引值：{{i}} --- 每一项的值：{{item}}</p>
</div>

data: {
    list: [1, 2, 3, 4, 5, 6]
}
```

* (2)循环对象数组

```
<div>
   <p v-for="(item, i) in list">索引值：{{i}} --- id值：{{item.id}} --- name值：{{item.name}}</p>
</div>

data: {
    list: [
       {id:1, name: 'hl01'},
       {id:2, name: 'hl02'},
       {id:3, name: 'hl03'},
       {id:4, name: 'hl04'},
    ]
}
```

* (3)循环对象

```
<div>
   <p v-for="(value, key, index) in userList">value值：{{ value }} --- key值：{{ key }} ---索引值：{{ index }}</p>
</div>

data: {
    userList: {
      id: 1,
      name: 'fqy',
      gender: '男'
    }
}
```

* (3)迭代数字

如果使用 v-for 迭代数字的话，count从 1 开始。

```
<div>
   <p v-for="count in 10">这是第 {{ count }}次循环</p>
</div>
```

#### v-for 指令使用注意事项：

**在V2.20+的版本里，当在组件中使用 v-for 时，必须指定 key值**。

当 Vue.js 用v-for正在更新已渲染过的元素列表时，它默认用“就地重用”策略，如果数据项的顺序被改变， Vue将不是移动 DOM 元素来匹配数据项的顺序，而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。

为了给 Vue 一个提示，以便它能够跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一key 属性。

* (1) v-for 循环的时候，key 属性只能使用 number 或者 string 类型的值；
* (2) key 在使用的时候，必须使用 v-bind 属性绑定的形式，指定 key 的值；
* (3) 在组件中或者某些特殊情况下，使用 v-for 循环时，必须指定 唯一的 string/number 类型的 :key 值。

```
<div id="app">
   <p v-for="item in list" :key="item.id">
   <input type="checkbox">
   {{item.id}} --- {{item.name}}
   </p>
</div>

data: {
    list: [
        {id: 1, name: '李斯'},
        {id: 2, name: '嬴政'},
        {id: 3, name: '赵高'},
        {id: 4, name: '韩非'},
        {id: 5, name: '荀子'},
    ]
}
```

### v-if 指令 和 v-show 指令

**v-if和v-show的区别**：v-if每次都会重新删除或创建元素；而v-show每次不会重新进行 DOM 的删除和创建操作，只是切换了元素的 display:none 样式。所以**v-if有较高的切换性能消耗，v-show有较高的初始渲染消耗**。

如果元素涉及到频繁的切换，最好不要使用 v-if，而是推荐使用 v-show。如果元素可能永远也不会被显示出来被用户看到，则推荐使用 v-if。







