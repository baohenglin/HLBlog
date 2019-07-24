## JavaScript简介

JavaScript 是互联网上最受欢迎的编程语言之一，用于为网页添加交互性，处理数据，以及创建各种应用程序（移动应用程序，桌面应用程序，游戏等）, 这门语言可用于 HTML 和 web，更可广泛用于服务器、PC、笔记本电脑、平板电脑和智能手机等设备。javascript因为兼容于ECMA标准，因此也称为ECMAScript。JavaScript作为一种脚本语言，已经被广泛地应用于Web页面当中，通过嵌入HTML来实现各种酷炫的动态效果，为用户提供赏心悦目的浏览效果。除此之外，也可以用于控制cookies以及基于Node.js技术进行服务器端编程。javascript是甲骨文公司的注册商标，完整的JavaScript实现包含三个部分：ECMAScript，文档对象模型和浏览器对象模型。JavaScript 是所有现代浏览器以及 HTML5 中的默认脚本语言。JavaScript可被放置在 HTML 页面的 <body> 和 <head> 部分中。

## JavaScript基本特点

* JavaScript 是一种轻量级的编程语言。
* JavaScript 是一种解释性脚本语言（代码不进行预编译）
* JavaScript 是可插入 HTML 页面的编程代码。
* JavaScript 插入 HTML 页面后，可由所有的现代浏览器执行。

## JavaScript组成部分

JavaScript由三部分组成，分别是ECMAScript、DOM(文档对象模型)、BOM(浏览器对象模型)。

* (1)ECMAScript描述了该语言的语法和基本对象。
* (2)文档对象模型（DOM），描述处理网页内容的方法和接口。
* (3)浏览器对象模型（BOM），描述与浏览器进行交互的方法和接口。

## JavaScript应用场景

* 浏览器方面：大部分公司的前端都在使用javascript来完成浏览器客户端的开发。
* 服务端方面：有些有大前端团队的公司开始使用nodejs做web服务端开发。
* 其他方面：  JavaScript还可以运用在APP、微信小程序、桌面应用、浏览器插件开发、游戏开发、VR、AR、硬件、操作系统等领域。

## JavaScript的使用

HTML 中的脚本必须位于 <script> 与 </script> 标签之间。脚本可被放置在 HTML 页面的 <body> 和 <head> 部分中，或者同时存在于两个部分中。通常的做法是把函数放入 <head> 部分中，或者放在页面底部。这样就可以把它们安置到同一处位置，不会干扰页面的内容。
  
如需在 HTML 页面中插入 JavaScript，请使用 <script> 标签。<script> 和 </script> 会告诉 JavaScript 在何处开始和结束。浏览器会解释并执行位于 <script> 和 </script>之间的 JavaScript 代码。那些老旧的实例可能会在 <script> 标签中使用 type="text/javascript"。现在已经不必这样做了。JavaScript 是所有现代浏览器以及 HTML5 中的默认脚本语言。
  
## JavaScript输出：

JavaScript 没有任何打印或者输出的函数。不过 JavaScript 可以通过不同的方式来输出数据：

* (1)使用 document.write() 方法将内容写到 HTML 文档中。

请使用 document.write() 仅仅向文档输出写内容。如果在文档已完成加载后执行 document.write，整个 HTML 页面将被覆盖。所谓的HTML文档加载，其实HTML文档是自上而下的加载HTML表示的內容，当整個页面內容都加载完毕之后，再调用document.write()这方法(function)会将显示的页面内容都清除掉，相当于重新加载。

* (2)使用 window.alert() 弹出警告框。
* (3)使用 innerHTML 写入到 HTML 元素。
* (4)使用 console.log() 写入到浏览器的控制台。

```
<script>
    a = 3;
    b = 4;
    c = a + b;
    console.log(c);
</script>
```

## JavaScript字面量

在JS中，一个字面量是一个常量，字面量的种类包括数字字面量、字符串字面量、表达式字面量、数组字面量、对象字面量、函数字面量。

* 数字（Number）字面量 可以是整数或者是小数，或者是科学计数(e)。
* 字符串（String）字面量 可以使用单引号或双引号。
* 表达式字面量 用于计算。如 5 + 6
* 数组（Array）字面量 定义一个数组。如：[40, 100, 1, 5, 25, 10]
* 对象（Object）字面量 定义一个对象。如：{firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"}
* 函数（Function）字面量 定义一个函数。如：

```
function myFunction(a, b) { return a * b;}
```

## JavaScript 变量

在编程语言中，变量用于存储数据值。JavaScript 使用关键字 var 来定义变量， 使用等号来为变量赋值。变量可以通过变量名访问。在指令式语言中，变量通常是可变的。字面量是一个恒定的值。

```
var x, length; 
x = 5; 
length = 6;
```

## JavaScript 操作符

操作符包括算数运算符("+ - * /")、赋值运算符("=")、位运算符(&、|、!)、条件运算符("==")、比较运算符("!="、">"、"<")等

## JavaScript 标识符

和其他任何编程语言一样，JavaScript 保留了一些标识符为自己所用。这些关键字在当前的语言版本中并没有使用，但在以后 JavaScript 扩展中会用到。

JavaScript 标识符必须以字母、下划线（_）或美元符（$）开始。后续的字符可以是字母、数字、下划线或美元符（数字是不允许作为首字符出现的，以便 JavaScript 可以轻易区分开标识符和数字）。
  


