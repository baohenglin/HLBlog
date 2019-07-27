## JavaScript简介

JavaScript 是互联网上最受欢迎的编程语言之一，用于为网页添加交互性，处理数据，以及创建各种应用程序（移动应用程序，桌面应用程序，游戏等）, 这门语言可用于 HTML 和 web，更可广泛用于服务器、PC、笔记本电脑、平板电脑和智能手机等设备。javascript因为兼容于ECMA标准，因此也称为ECMAScript。JavaScript作为一种脚本语言，已经被广泛地应用于Web页面当中，通过嵌入HTML来实现各种酷炫的动态效果，为用户提供赏心悦目的浏览效果。除此之外，也可以用于控制cookies以及基于Node.js技术进行服务器端编程。javascript是甲骨文公司的注册商标，完整的JavaScript实现包含三个部分：ECMAScript，文档对象模型和浏览器对象模型。JavaScript 是所有现代浏览器以及 HTML5 中的默认脚本语言。JavaScript可被放置在 HTML 页面的 <body> 和 <head> 部分中。

## JavaScript基本特点

* JavaScript 是一种轻量级的编程语言。
* JavaScript 是一种解释性脚本语言（代码不进行预编译）
* JavaScript 是可插入 HTML 页面的编程代码。
* JavaScript 插入 HTML 页面后，可由所有的现代浏览器执行。
* JavaScript 对大小写敏感。
* 在 JavaScript 中，用分号来结束语句是可选的（也就是可以写分号也可以不写）。
* JavaScript 会忽略多余的空格。您可以向脚本添加空格，来提高其可读性。
* 可以在文本字符串中使用反斜杠对代码行进行换行。

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

在编程语言中，变量用于存储数据值。JavaScript 使用关键字 var 来定义变量， 使用等号来为变量赋值。变量可以通过变量名访问。在指令式语言中，变量通常是可变的。字面量是一个恒定的值。JavaScript 中，常用驼峰法的命名规则来命名变量。JavaScript具有隐含的全局概念，意味着你不声明的任何变量都会成为一个全局对象属性。

变量也能以 $ 和 _ 符号开头（不过我们不推荐这么做），一般变量以字母开头，变量名称对大小写敏感（y 和 Y 是不同的变量）。

```
var x, length; 
x = 5; 
length = 6;
```

可以在一条语句中声明很多变量。该语句以 var 开头，并使用逗号分隔变量即可。

```
var lastname="Doe", age=30, job="carpenter";
```

声明也可横跨多行：  

```
var lastname="Doe",        
age=30,        
job="carpenter";
```

在计算机程序中，经常会声明无值的变量。未使用值来声明的变量，其值实际上默认是 undefined。如果重新声明 JavaScript 变量，该变量的值不会丢失。在以下两条语句执行后，变量 carname 的值依然是 "Volvo"： 

```
var carname="Volvo";        
var carname;
```

全局变量是指在函数体外定义的变量或者在函数体内定义的无var的变量。全局变量可以在任意位置调用。

局部变量是指在函数体内通过关键字var定义的变量。局部变量的作用域是当前函数体内部。

变量作用域优先级是局部变量高于同名全局变量；参数变量高于同名全局变量；局部变量高于同名参数变量。

作用域链规则是内层函数可以访问外层函数的局部变量，但是外层函数不能访问内层函数的局部变量。

## JavaScript 操作符

操作符包括算数运算符("+ - * /")、赋值运算符("=")、位运算符(&、|、!)、条件运算符("==")、比较运算符("!="、">"、"<")等

## JavaScript 标识符

和其他任何编程语言一样，JavaScript 保留了一些标识符为自己所用。这些关键字在当前的语言版本中并没有使用，但在以后 JavaScript 扩展中会用到。

JavaScript 标识符必须以字母、下划线（_）或美元符（$）开始。后续的字符可以是字母、数字、下划线或美元符（数字是不允许作为首字符出现的，以便 JavaScript 可以轻易区分开标识符和数字）。

## JS数据类型：

JS数据类型包括字符串（String）、数字(Number)、布尔(Boolean)、数组(Array)、对象(Object)、空（Null）、未定义（Undefined）。JavaScript 拥有动态类型。这意味着相同的变量可用作不同的类型。

```
var x;               // x 为 undefined
var x = 5;           // 现在 x 为数字
var x = "John";      // 现在 x 为字符串
```

JavaScript 布尔只能有两个值：true 或 false。

**JavaScript 数组**

下面的代码创建名为 cars 的数组:

```
var cars=new Array();
 cars[0]="Saab";
 cars[1]="Volvo";
 cars[2]="BMW";
```

或者(condensed array)，如下：

```
var cars=new Array("Saab","Volvo","BMW");
```

或者(literal array)

```
var cars=["Saab","Volvo","BMW"];
```

**JavaScript 对象**

对象由花括号分隔。在括号内部，对象的属性以名称和值对的形式 (name : value) 来定义。属性由逗号分隔。

```
ar person={firstname:"John", lastname:"Doe", id:5566};
```

对象属性有两种寻址方式：

```
name=person.lastname;
name=person["lastname"];
```

**Undefined 和 Null

Undefined 这个值表示变量不含有值。使用var声明但未初始化的变量的默认值是Undefined。

null逻辑上表示一个空对象的指针。使用typeof检测时返回object。

由于undefined派生于null，因此在使用"=="比较时会返回ture。

**声明变量类型**

当您声明新变量时，可以使用关键词 "new" 来声明其类型。JavaScript 变量均为对象。当您声明一个变量时，就创建了一个新的对象。

```
var carname=new String;
 var x=      new Number;
 var y=      new Boolean;
 var cars=   new Array;
 var person= new Object;
```

## JavaScript 函数

在JavaScript中，函数即对象，可以随意地被程序操控，函数可以嵌套在其他函数中定义，这样可以访问它们被定义时所处的作用域中的任何变量。函数是由事件驱动的或者当它被调用时执行的可重复使用的代码块。函数就是包裹在花括号中的代码块，使用关键词 function来声明函数。

```
function functionname()
 {
    执行代码
 }
```

当调用该函数时，会执行函数内的代码。可以在某事件发生时直接调用函数（比如当用户点击按钮时），并且可由 JavaScript 在任何位置进行调用。JavaScript 对大小写敏感。关键词 function 必须是小写的，并且必须以与函数名称相同的大小写来调用函数。function 中的花括号是必需的，即使函数体内只包含一条语句，仍然必须使用花括号将其括起来。

## 调用带参数的函数

在调用函数时，您可以向其传递值，这些值被称为参数。这些参数可以在函数中使用。如果有多个参数，可以使用逗号 (,) 分隔。

```
function myFunction(var1,var2)
{
    //函数体
}
```

## 带有返回值的函数

使用 return 语句时，函数会停止执行，并返回指定的值。

```
function myFunction(a,b)
{
  var x=a*b;
  return x;
}
```

## 局部 JavaScript 变量、全局JavaScript变量及变量的声明周期

在 JavaScript 函数内部声明的变量（使用 var）是局部变量，所以只能在函数内部访问它。（该变量的作用域是局部的）。可以在不同的函数中使用名称相同的局部变量，因为只有声明过该变量的函数才能识别出该变量。局部变量比同名全局变量的优先级高，所以局部变量会隐藏同名的全局变量。

在函数外声明的变量是全局变量，网页上的所有脚本和函数都能访问它。

JavaScript 变量的生命期从它们被声明的时间开始。局部变量会在函数运行以后被删除。全局变量会在页面关闭后被删除。

需要注意的是，如果您把值赋给尚未声明的变量，该变量将被自动作为全局变量声明。

```
carname="Volvo";
```

将声明一个全局变量 carname，即使它在函数内执行。

在 ES6 中，提供了 let 关键字和 const 关键字。let 的声明方式与 var 相同，用 let 来代替 var 来声明变量，就可以把变量限制在当前代码块中。使用 const 声明的是常量，其值一旦被设定便不可被更改。


## JavaScript 事件

事件是可以被 JavaScript 侦测到的行为。HTML 事件是发生在 HTML 元素上的事情。当在 HTML 页面中使用 JavaScript 时， JavaScript 可以触发这些事件。HTML 事件可以是浏览器行为，也可以是用户行为。HTML 网页中的每个元素都可以产生某些可以触发 JavaScript 函数的事件。以下是 HTML 事件的实例：

* HTML 页面完成加载
* HTML input 字段改变时
* HTML 按钮被点击

在事件触发时 JavaScript 可以执行一些代码。HTML 元素中可以添加事件属性，使用 JavaScript 代码来添加 HTML 元素。

下面例子中，JavaScript 代码将修改 id="demo" 元素的内容。

```
<button onclick="getElementById('demo').innerHTML=Date()">现在的时间是?</button>
<p id="demo"></p>
```

下面例子，代码将修改自身元素的内容 (使用 this.innerHTML):

```
<button onclick="this.innerHTML=Date()">现在的时间是?</button>
```

JavaScript代码通常是几行代码。比较常见的是通过事件属性来调用：

```
<p>点击按钮执行 <em>displayDate()</em> 函数.</p>
<button onclick="displayDate()">点我</button>

<script>
function displayDate()
{
	document.getElementById("demo").innerHTML=Date();
}
</script>
<p id="demo"></p>
```

## 常见的HTML事件:

* onchange      HTML 元素改变
* onclick       用户点击 HTML 元素
* onmouseover   用户在一个HTML元素上移动鼠标
* onmouseout    用户从一个HTML元素上移开鼠标
* onkeydown     用户按下键盘按键
* onload        浏览器已完成页面的加载


## JavaScript 字符串

在 JavaScript 中，字符串写在单引号或双引号中。可以使用反斜杠 (\) 来转义 "Vikings" 字符串中的双引号，如下:

```
"We are the so-called \"Vikings\" from the north." 
```

通常， JavaScript 字符串是原始值，可以使用字符创建： var firstName = "John"。但我们也可以使用 new 关键字将字符串定义为一个对象：

```
var firstName = new String("John")
```

但是一般不要创建 String 对象。它会拖慢执行速度，并可能产生其他副作用。

**字符串属性和方法**

原始值字符串，如 "John", 没有属性和方法(因为他们不是对象)。原始值可以使用 JavaScript 的属性和方法，因为 JavaScript 在执行方法和属性时可以把原始值当作对象。

**字符串属性**：

* constructor	返回创建字符串属性的函数
* length		返回字符串的长度
* prototype	允许您向对象添加属性和方法


**字符串方法**如下:

* charAt()			返回指定索引位置的字符
* charCodeAt()			返回指定索引位置字符的 Unicode 值
* concat()			连接两个或多个字符串，返回连接后的字符串
* fromCharCode()		将指定的 Unicode 值转换为字符串
* indexOf()			返回字符串中检索指定字符第一次出现的位置
* lastIndexOf()			返回字符串中检索指定字符最后一次出现的位置
* localeCompare()		用本地特定的顺序来比较两个字符串
* match()			找到一个或多个正则表达式的匹配
* replace()			替换与正则表达式匹配的子串
* search()			检索与正则表达式相匹配的值
* slice()			提取字符串的片断，并在新的字符串中返回被提取的部分
* split()			把字符串分割为子字符串数组
* substr()			从起始索引号提取字符串中指定数目的字符
* substring()			提取字符串中两个指定的索引号之间的字符
* toLocaleLowerCase()		根据主机的语言环境把字符串转换为小写，只有几种语言（如土耳其语）具有地方特有的大小写映射
* toLocaleUpperCase()		根据主机的语言环境把字符串转换为大写，只有几种语言（如土耳其语）具有地方特有的大小写映射
* toLowerCase()			把字符串转换为小写
* toString()			返回字符串对象值
* toUpperCase()			把字符串转换为大写
* trim()			移除字符串首尾空白
* valueOf()			返回某个字符串对象的原始值

## JavaScript 运算符

* 算术运算符： +、 -、 *、 /、 %、++(自增)、--(自减) 
* 赋值运算符： +=、-=、*=、/=、%=
* 字符串连接运算符： "+"用于把文本值或字符串变量加起来（连接起来）
* 关系运算符：<、<=、>、>=、==、！=、===(比较两个运算数的返回值和数据类型是否相同)、!==(比较两个运算数的返回值和数据类型是否不同)
* 逻辑运算符：&&、||、!。JavaScript逻辑运算符的优先级是：！、&& 、||。
* 对象运算符：in、instanceof、new、delete、.和[]、()
* 位运算符：&、|
* 条件运算符(三元运算符)：?:

## JavaScript 类型转换

在 JavaScript 中有 5 种不同的数据类型：

* string
* number
* boolean
* object
* function

3 种对象类型：

* Object
* Date
* Array

2 个不包含任何值的数据类型：

* null
* undefined

**typeof 操作符**：可以使用 typeof 操作符来查看 JavaScript 变量的数据类型。

NaN 的数据类型是 number。数组(Array)的数据类型是 object。日期(Date)的数据类型为 object。null 的数据类型是 object。未定义变量的数据类型为 undefined。如果对象是 JavaScript Array 或 JavaScript Date ，我们就无法通过 typeof 来判断他们的类型，因为都是 返回 Object。

**constructor 属性**：constructor 属性返回所有 JavaScript 变量的构造函数。

可以使用 constructor 属性来查看对象是否为数组 (包含字符串 "Array")。代码如下：

```
<p>判断是否为数组。</p>
<p id="demo"></p>
<script>
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = isArray(fruits);
function isArray(myArray) {
    return myArray.constructor.toString().indexOf("Array") > -1;
}
</script>
```

可以使用 constructor 属性来查看是对象是否为日期 (包含字符串 "Date")，代码如下：

```
<p>判断是否为日期。</p>
<p id="demo"></p>
<script>
var myDate = new Date();
document.getElementById("demo").innerHTML = isDate(myDate);
function isDate(myDate) {
    return myDate.constructor.toString().indexOf("Date") > -1;
}
</script>
```
**将数字转换为字符串**

全局方法 String() 可以将数字转换为字符串。该方法可用于任何类型的数字，字母，变量，表达式。

```
<p> String() 方法可以将数字转换为字符串。</p>
<p id="demo"></p>
<script>
var x = 123;
document.getElementById("demo").innerHTML =
    String(x) + "<br>" +
    String(123) + "<br>" +
    String(100 + 23);
</script>
```
Number 方法 toString() 也是有同样的效果，可以将数字转换为字符串。

```
x.toString() 
(123).toString() 
(100 + 23).toString()
```

* toExponential()	把对象的值转换为指数计数法。
* toFixed()		把数字转换为字符串，结果的小数点后有指定位数的数字。
* toPrecision()		把数字格式化为指定的长度。

**将布尔值转换为字符串**

全局方法 String() 可以将布尔值转换为字符串。

```
String(false)        // 返回 "false"
String(true)         // 返回 "true"
```

Boolean 方法 toString() 也有相同的效果。

```
false.toString()     // 返回 "false"
true.toString()      // 返回 "true"
```

**将日期转换为字符串**

全局方法 String() 可以将日期转换为字符串:

```
String(Date())
```

日期转换为字符串的函数包括以下这些：

* getDate()		从 Date 对象返回一个月中的某一天 (1 ~ 31)。
* getDay()		从 Date 对象返回一周中的某一天 (0 ~ 6)。
* getFullYear()		从 Date 对象以四位数字返回年份。
* getHours()		返回 Date 对象的小时 (0 ~ 23)。
* getMilliseconds()	返回 Date 对象的毫秒(0 ~ 999)。
* getMinutes()		返回 Date 对象的分钟 (0 ~ 59)。
* getMonth()		从 Date 对象返回月份 (0 ~ 11)。
* getSeconds()		返回 Date 对象的秒数 (0 ~ 59)。
* getTime()		返回 1970 年 1 月 1 日至今的毫秒数。

**将字符串转换为数字**

全局方法 Number() 可以将字符串转换为数字。

```
Number("3.14")    // 返回 3.14
Number(" ")       // 返回 0 
Number("")        // 返回 0 
Number("99 88")   // 返回 NaN
```

其他字符串转为数字的方法：

```
parseFloat()	解析一个字符串，并返回一个浮点数。
parseInt()	解析一个字符串，并返回一个整数。
```

将布尔值转换为数字：全局方法 Number() 可将布尔值转换为数字。

```
Number(false)     // 返回 0
Number(true)      // 返回 1
```

**将日期转换为数字**：全局方法 Number() 可将日期转换为数字。

```
d = new Date(); 
Number(d)          // 返回 1404568027739
```

日期方法 getTime() 也有相同的效果。

```
d = new Date(); 
d.getTime()        // 返回 1404568027739
```

自动转换类型 Type Conversion：当 JavaScript 尝试操作一个 "错误" 的数据类型时，会自动转换为 "正确" 的数据类型。

自动转换为字符串：当你尝试输出一个对象或一个变量时 JavaScript 会自动调用变量的 toString() 方法。

```
document.getElementById("demo").innerHTML = myVar; 

// if myVar = {name:"Fjohn"}  // toString 转换为 "[object Object]"
// if myVar = [1,2,3,4]       // toString 转换为 "1,2,3,4"
// if myVar = new Date()      // toString 转换为 "Fri Jul 18 2014 09:08:55 GMT+0200"
```

数字和布尔值也经常相互转换:

```
// if myVar = 123             // toString 转换为 "123"
// if myVar = true            // toString 转换为 "true"
// if myVar = false           // toString 转换为 "false"
```




