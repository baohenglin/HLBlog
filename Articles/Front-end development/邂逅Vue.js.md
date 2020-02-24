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

### 1 Vue 简介

Vue 是一套用于构建用户界面的渐进式框架。那么如何理解“渐进式”呢？

* 渐进式意味着可以将 Vue 作为应用的一部分嵌入其中，以带来更丰富的交互体验；
* 如果希望将更多的业务逻辑使用 Vue 实现，那么可以将 Vue 的核心库以及其生态系统(Core + Vue-router + Vuex)集成到项目中以满足各种各样的需求。

### 2 Vue 特点

* (1)解耦视图和数据
* (2)可复用的组件
* (3)前端路由技术
* (4)状态管理
* (5)虚拟 DOM

### 3 Vue 安装

* (1)CDN 引入
* (2)下载方式引入
* (3)利用 npm 安装

【注意】**ES6中使用 let 关键字定义变量，使用 const 关键字定义常量**


### 4 Vue 中的 MVVM

* M层： Model层,即数据层。具体代码中指的是 Vue 实例中 data 属性所对应的 Value值。可以是本地数据，也可以是请求自服务器的数据。
* V层： View层，即视图层。在前端开发中，通常是指 DOM 层。其主要作用就是给用户展示各种信息。
* VM层： VueModel层，即视图模型层。具体代码中指的是我们创建的 Vue 实例对象。它是 View层和 Model 层之间的调度者。VueModel 层的主要作用是一方面它实现了 Data Binding，也就是数据绑定，将 Model 的改变实时地反映到View中。另一方面它实现了 DOM listener，也就是 DOM 监听，当 DOM 发生一些事件（比如点击、滚动、touch等）时，可以监听到，并在需要的情况下改变对应的 Data。


### 5 创建 Vue实例传入的 optios

* (1)el:

  ✅类型： **string | HTMLElement**
  
  ✅作用：**挂载将要管理的 DOM。即决定之后 Vue 实例会管理哪一个 DOM**。
  
* (2)data:

  ✅类型：**Object | Function(组件当中data必须是一个函数)**
  
  ✅作用：**Vue实例对应的数据对象**。
  
* (3)methods:

  ✅类型：**{[key:string]:Function}**
  
  ✅作用：**定义属于Vue的一些方法，以供其他地方调用，也可以在指令中使用**
  
* (4)Vue 的生命周期函数。


### 6 Vue 的生命周期（钩子函数）

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


### 7 Vue 基本语法

#### 7-1. Mustache 语法（双大括号语法/插值操作）

【注意】标签之间的内容显示可以使用 Mustache 语法，但是标签内部不能使用 Mustache 语法。

```
<h2>{{message}}</h2>
<h2>{{message}}，中国加油！</h2>
<h2>{{firstName + '' + lastName}}</h2>
<h2>{{firstName + '' + lastName}}</h2>
<h2>{{firstName}} {{lastName}}</h2>
<h2>{{counter * 2}}</h2>
```

#### 7-2. v-once 指令

v-once指令：**该指令表示元素和组件只渲染一次，不会随着数据的改变而改变**。该指令后面不需要跟任何表达式。

应用场景：在某些情况下，我们可能不希望界面响应式地随数据随意改变，此时可以使用 v-once 指令。

```
<h2 v-once>{{message}}</h2>
```

#### 7-3. v-html 指令

```
<h2 v-html="<a href='http://www.baidu.com'>百度一下</a>"></h2>
```

#### 7-4. v-text 指令

v-text指令作用与 Mustache 比较相似，都是用于将数据显示在界面中。但是 v-text 与 Mustache相比不够灵活，会将原有的内容替换掉。

```
<h2 v-text="message"></h2>
```

#### 7-5. v-pre 指令

```
<h2>{{message}}</h2>        //会解析 message
<h2 v-pre>{{message}}</h2>  //不会解析 message，原封不动的展示 ‘{{message}}’
```

#### 7-6. v-cloak 指令

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

### 8 v-bind 指令：

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

**8-1 v-bind 动态绑定 class 属性**

* class 对象语法

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

* class 数组语法（动态绑定一个数组）

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

**8-2 v-bind 动态绑定 style 样式（组件化开发中使用）**

我们可以利用 v-bind:style 来绑定一些 CSS 内联样式。在写 CSS 属性名的时候，比如 font-size，我们可以使用驼峰式(fontSize)，也可以使用短横线式，使用短横线式，必须使用单引号括起来('font-size')。

* 对象语法

```
<div>
// <h2 :style="{key(属性名): value(属性值)}">{{message}}</h2>
// 属性值 50px 必须加上单引号，否则会当成一个变量去解析。
<h2 :style="{fontSize: '50px', color: finalColor}">{{message}}</h2>
<h2 :style="{fontSize: finalFontSize + 'px', color: finalColor}">{{message}}</h2>
</div>

<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '中国加油',
      finalFontSize: 100,
      finalColor: 'red'
    }
  })
</script>
```

* 数组语法 (用的比较少)

```
<div>
<h2 :style="[baseStyle1, baseStyle2]">{{message}}</h2>
</div>

<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '中国加油',
      baseStyle1: {backgroudColor: 'red'},
      baseStyle2: {fontSize: '100px'}
    }
  })
</script>
```

### 9 Vue 的计算属性（很重要）

#### 9-1 计算属性 computed 和 methods 的区别：

**计算属性computed 只会调用一次，因为 computed会缓存；methods 中的方法被调用几次就会实际调用几次，没有缓存。所以 使用 computed 的性能更高**。

```
<div id="app">
<h2>{{getFullName()}}</h2>
// 计算属性 不用加小括号
<h2>{{fullName}}</h2>
</div>

<script>
  const app = new Vue({
    el: '#app',
    data: {
      firstName: 'bao',
      lastName: 'henglin'
      
    },
    methods: {
        getFullName() {
          return this.firstName + ' ' + this.lastName;
        }
    },
    // 计算属性
    computed: {
      fullName: function () {
        return this.firstName + ' ' + this.lastName;
      }
    }
  })
</script>
```

#### 9-2 计算属性应用场景：计算总价格

```
<div id="app">
<h2>{{totalPrice}}</h2>
</div>

<script>
  const app = new Vue({
    el: '#app',
    data: {
      books: [
        {id: '1000', name: 'Unix编程艺术', price: '110'},
        {id: '1001', name: '代码大全', price: '90'},
        {id: '1002', name: '深入理解计算机原理', price: '150'},
        {id: '1003', name: '现代操作系统', price: '100'},
      ]
    },
    methods: {
 
    },
    // 计算属性
    computed: {
      totalPrice: function () {
        let result = 0;
        //ES5 for循环语法：
        for(let i = 0; i < this.books.lenght; i++) {
           result += this.books[i].price;
        }
        return result;
      }
    }
  })
</script>
```

ES6 for循环语法1：

```
for (let i in this.books) {
  result += this.books[i].price;
}
```

ES6 for循环语法2：(**推荐**)

```
for (let book of this.books) {
  result += book.price;
}
```

### 9-3 计算属性的 setter 和 getter 方法

```
<div id="app">
//方案1：Mustache语法
<h2>{{firstName}} {{lastName}}<h2/>
//方案2：通过定义methods方法
<h2>{{getFullName()}}</h2>
//方案3：计算属性
<h2>{{fullName}}</h2>
</div>

<script>
  const app = new Vue({
    el: '#app',
    data: {
      firstName: 'bao',
      lastName: 'henglin'
      
    },
    methods: {
      getFullName() {
        return this.firstName + ' ' + this.lastName;
      }
    },
    // 计算属性的完整写法(setter方法 + getter方法)，但是计算属性是只读属性，一般情况下，不写 setter方法。
    computed: {
      fullName: {
        //set: function {},
        get: function {
          return this.firstName + ' ' + this.lastName;
        }
      }
    }
  })
</script>
```

【注意】推荐使用方案3：计算属性方案。**由于计算属性存在缓存机制，所以当多次调用时，实际上计算属性computed 只会执行一次；而methods方法没有缓存机制。因此二者之中 使用 computed 的性能更高**。

## 10 ES6 部分语法

### 10-1 let/var 关键字

变量作用域：变量在什么范围内可用。

没有块级作用域引起的问题：

```
// if 块级
var func;
if(true) {
   var name = 'bao';
   func = function () {
    console.log(name);
   }
   func();
}

name = 'he';//没有块级作用时，name在其他地方可以被修改，进而导致不可预测的问题。
func();
console.log(name);
```

```
// 错误的方式
// for 块级：当打印时，i的值已经变成最后一次循环的值了。
var btns = document.getElementsByTagName('button');
for(var i = 0; i<btns.lenght; i++) {
   btns[i].addEventListener('click', function () {
    console.log('第' + i + '个btn点击');
   });
}
```

* ES5的闭包解决方案：

```
// 为什么闭包可以解决这个问题：**因为函数是有作用域的**。
var btns = document.getElementsByTagName('button');
for(var i = 0; i<btns.lenght; i++) {
  (function (i) {
    btns[i].addEventListener('click', function () {
    console.log('第' + i + '个btn点击');
   });
  })(i)
}
```

* ES6的 let 解决方案

```
const btns = document.getElementsByTagName('button');
for(let i = 0; i<btns.lenght; i++) {
   btns[i].addEventListener('click', function () {
   console.log('第' + i + '个btn点击');
}
```

**在ES6之前，因为使用var关键字定义的变量在 if和for中都是没有块级作用域的，所以我们必须借助闭包(function的作用域)来解决访问外部变量作用域的问题。而在ES6中，加入了 let，使用 let关键字定义的变量在if和for中是有块级作用域的**。

**ES5中的var是没有块级作用域的（比如if/for）;ES6中的let是有块级作用域的（比如if/for）**。

### 10-2 ES6 const 关键字

**const 关键字的作用**：const 关键字在很多语言中已经存在。比如在 C、C++ 中，主要作用是**将变量修饰为常量**。在 JavaScript 也是如此，使用 const 关键字修饰的标识符是常量，不可以再次赋值。

**const 关键字的使用场景**：当我们修饰的标识符不会被再次赋值时，就可以使用 const 来保证数据的安全性。

**建议**：在 ES6 开发中，优先使用 const。只有在需要改变某一个标识符时才使用 let。

**注意事项**：

* (1)一旦给 const 修饰的标识符赋值，就不能修改。
* (2)在使用 const 定义标识符时，必须进行赋值
* (3)常量的含义是：指向的对象不能修改，但是可以修改对象内部的属性。

```
const obj = {
  name: 'bao',
  age: 22,
  height: '1.87'
}
obj.name = 'Bao',
obj.age = 23,
obj.height = 1.90
```

### 10-3 ES6 对象字面量的增强写法

* （1）属性的增强写法：

```
const name = 'bao',
const age = '18',
const height = 1.88

//ES5的写法：
const obj = {
  name: name,
  age: age,
  height: height
}

//ES6的写法：
const obj = {
  name,
  age,
  height
}
```

* （2）函数的增强写法：

```
const obj = {
  // ES5的写法
  run: function () {
  
  }
  //ES6函数的增强写法
  run() {

  }
}

```

## 11 Vue的事件监听 v-on指令

当监听某个操作（比如点击、拖拽、键盘事件(keyup)等）时，需要使用 v-on 指令来监听。

* v-on作用：绑定事件监听器。
* v-on缩写(语法糖)：@
* 预期：function | Inline Statement | Object
* 参数：event

### 11-1 v-on 传参问题分析

```
//情况1：方法不带参数时，调用方法不需要在方法后面加小括号
<button @click='btn1Click()'>按钮1</button>
<button @click='btn1Click'>按钮1</button>

methods: {
  btn1Click() {
  
  }
}
```

```
//情况2：方法本身需要一个参数，但是调用时并没有省略了小括号，此时 Vue 会默认将浏览器生成的event事件对象作为参数传递给方法的形参。
<button @click='btn2Click'>按钮2</button>

methods: {
  btn2Click(parameter) {
  
  }
}
```

```
//情况3：方法定义时除了需要传 event对象 参数外，还需要传其他参数。
// 如何手动获取到浏览器的event对象？使用“$event”来获取
<button @click='btn3Click('abc', $event)'>按钮3</button>

methods: {
  btn2Click(parameter, event) {
  
  }
}
```

### 11-2 v-on 修饰符

* .stop修饰符：阻止冒泡问题

```
//冒泡问题
<div id="app">
  <div @click='divClick'>
    <button @click.stop='btnClick'>按钮</button>
  </div>
</div>

<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '中国加油🇨🇳'
    },
    methods: {
      divClick() {
      
      },
      btnClick() {
      
      }
    },
    // 计算属性
    computed: {
      
    }
  })
</script>
```

* .prevent 修饰符：阻止默认行为。
* .native 修饰符：监听组件根元素的原生事件。
* .once 修饰符：只触发一次回调。
* .keyCode 修饰符：监听键盘上的某个键。

```
//监听键盘上的 enter 按键
<input type='text' @keyup.enter='keyupClick'>
```

## 11 Vue的条件判断 v-if & v-else-if & v-else

```
<div id='app'>
  <p v-if="score>=90">优秀</p>
  <p v-else-if="score>=80">良好</p>
  <p v-else-if="score>=60">及格</p>
  <p v-else>不及格</p>
</div>
```
### 11-1 v-show 指令

v-if和v-show的区别：**对于v-if来说，当条件为false时，压根不会有对应的元素在 DOM 中；对于 v-show 来说，当条件为 false 时，仅仅是将元素的 行内样式 display 属性设置为 none 而已，元素依然存在于 DOM 中**。

那么开发中到底选择 v-if 还是 v-show 呢？**当需要在显示和隐藏之间频繁切换时，应该选择v-show；当只有一次切换时，选择 v-if**。

## 12 v-for 指令

* 遍历数组

```
<div id='app'>
  <ul>
    <li v-for="(item, index) in booksArr">{{item.name}}</li>
  </ul>
</div>

<script>
  const app = new Vue({
    el: '#app',
    data: {
      booksArr: [
        {id: '1000', name: 'Unix编程艺术', price: '110'},
        {id: '1001', name: '代码大全', price: '90'},
        {id: '1002', name: '深入理解计算机原理', price: '150'},
        {id: '1003', name: '现代操作系统', price: '100'},
      ]
    }
  })
</script>
```
* 遍历对象

```
<div id='app'>
  <ul>
    <li v-for="(value, key) in infor">{{value}}---{{key}}</li>
    //遍历对象也可以获取 index
    <li v-for="(value, key, index) in infor">{{value}}---{{key}}---{{index}}</li>
  </ul>
</div>

<script>
  const app = new Vue({
    el: '#app',
    data: {
      infor: {
        id: '1000', 
        name: 'Unix编程艺术', 
        price: '110'
      },
    }
  })
</script>
```

### 12-1 组件的 key 属性

**官方推荐我们在使用 v-for 时，需要给对应的元素或组件添加一个 key 属性**。

那么为什么需要添加这个 key 属性呢？**key 属性的作用是为了更加高效地更新虚拟 DOM**。这就涉及到 Vue 的虚拟 DOM 和 Diff 算法。当某一层有很多相同的节点时，比如现有A、B、C、D、E节点，我们希望在B和C之间插入一个F节点，Diff算法默认把C更新为F，把D更新为C，把E更新为D，最后再插入E，这样一来效率是很低的。当我们添加了key属性后，每个节点都会存在一个自身的唯一标识，这样 Diff算法就可以正确识别每一个节点，进而在正确的位置插入新的节点。




