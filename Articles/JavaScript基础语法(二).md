## JavaScript 函数

JavaScript 使用关键字 function 定义函数。函数声明后不会立即执行，会在我们需要的时候调用到。

```
function functionName(parameters) {        
  执行的代码        
}
```

函数除了可以通过声明定义，也可以是一个表达式。

函数表达式可以存储在变量中：

```
<body>

<p>函数可以存储在变量中:</p>
<p id="demo"></p>
<script>
var x = function (a, b) {return a * b};
document.getElementById("demo").innerHTML = x;
</script>

</body>
```

在函数表达式存储在变量后，变量也可作为一个函数使用：

```
<body>
<p>函数存储在变量后，变量可作为函数使用：</p>
<p id="demo"></p>
<script>
var x = function (a, b) {return a * b};
document.getElementById("demo").innerHTML = x(4, 3);
</script>
</body>
```

以上函数实际上是一个 匿名函数 (函数没有名称)。函数存储在变量中，不需要函数名称，通常通过变量名来调用。

**Function() 构造函数**

函数除了通过关键字 function 定义，函数同样可以通过内置的 JavaScript 函数构造器（Function()）定义。

```
<body>
<p>JavaScrip 内置构造函数。</p>
<p id="demo"></p>
<script>
var myFunction = new Function("a", "b", "return a * b");
document.getElementById("demo").innerHTML = myFunction(4, 3);
</script>
</body>
```

实际上，不必使用构造函数。上面实例可以写成：

```
<body>
<p id="demo"></p>
<script>
var myFunction = function (a, b) {return a * b}
document.getElementById("demo").innerHTML = myFunction(4, 3);
</script>
</body>
```

【注意】在 JavaScript 中，很多时候，你需要避免使用 new 关键字。

**函数提升（Hoisting）**

提升（Hoisting）是 JavaScript 默认将当前作用域提升到前面去的的行为。提升（Hoisting）应用在变量的声明与函数的声明。因此，函数可以在声明之前调用：

```
myFunction(5);               
function myFunction(y) {        
    return y * y;        
}
```

【注意】使用表达式定义函数时无法提升。

**自调用函数**

函数表达式可以 "自调用"。自调用表达式会自动调用。如果表达式后面紧跟 () ，则会自动调用。

```
<body>

<p>函数可以自动调用：</p>
<p id="demo"></p>
<script>
(function () {
    document.getElementById("demo").innerHTML = "Hello! 我是自己调用的";
})();
</script>

</body>
```

以上函数实际上是一个 匿名自我调用的函数 (没有函数名)。

**函数可以作为一个值使用**

JavaScript函数可以作为一个值使用：

```
<body>
<p>函数可作为一个值：</p>
<p>x = myFunction(4,3) 或 x = 12</p>
<p>两种情况下，x 的值都为 12。</p>
<p id="demo"></p>
<script>
function myFunction(a, b) {
    return a * b;
}
var x = myFunction(4, 3);
document.getElementById("demo").innerHTML = x;
</script>
</body>
```

JavaScript 函数可作为表达式使用：

```
<body>

<p>函数可作为一个表达式使用。</p>
<p id="demo"></p>
<script>
function myFunction(a, b) {
    return a * b;
}
var x = myFunction(4, 3) * 2;
document.getElementById("demo").innerHTML = x;
</script>

</body>
```

**函数是对象**

在 JavaScript 中使用 typeof 操作符判断函数类型将返回 "function" 。但是，将JavaScript 函数描述为一个对象更加准确。JavaScript 函数有 属性 和 方法。arguments.length 属性返回函数调用过程接收到的参数个数：

```
<body>
<p> arguments.length 属性返回函数接收到参数的个数：</p>
<p id="demo"></p>
<script>
function myFunction(a, b) {
    return arguments.length;
}
document.getElementById("demo").innerHTML = myFunction(4, 3);
</script>
</body>
```

toString() 方法将函数作为一个字符串返回:

```
<body>
<p> toString() 将函数作为一个字符串返回：</p>
<p id="demo"></p>
<script>
function myFunction(a, b) {
    return a * b;
}
document.getElementById("demo").innerHTML = myFunction(8,8).toString();
</script>
</body>
```

函数定义作为对象的属性，称之为对象方法。 函数如果用于创建新的对象，称之为对象的构造函数。


## JavaScript 函数参数

JavaScript 函数对参数的值(arguments)没有进行任何的检查。JavaScript 函数参数与大多数其他语言的函数参数的区别在于：它不会关注有多少个参数被传递，不关注传递的参数的数据类型。

**函数显式参数与隐藏参数(arguments)**

函数的显式参数：函数显式参数在函数定义时列出。函数隐藏参数(arguments)在函数调用时传递给函数真正的值。

```
function functionName(parameter1, parameter2, parameter3) {
    code to be executed
}
```

**参数规则**:

```
JavaScript 函数定义时参数没有指定数据类型。
JavaScript 函数对隐藏参数(arguments)没有进行检测。
JavaScript 函数对隐藏参数(arguments)的个数没有进行检测。
```

如果函数在调用时缺少参数，参数会默认设置为： undefined。有时这是可以接受的，但是建议最好为参数设置一个默认值。

```
<body>

<p>设置参数的默认值。</p>
<p id="demo"></p>
<script>
function myFunction(x, y) {
    if (y === undefined) {
        y = 0;
    }    
    return x * y;
}
document.getElementById("demo").innerHTML = myFunction(4);
</script>

</body>
```

如果函数调用时设置了过多的参数，参数将无法被引用，因为无法找到对应的参数名。 只能使用 arguments 对象来调用。

**Arguments 对象**

JavaScript 函数有个内置的对象 arguments 对象。argument 对象包含了函数调用的参数数组。通过这种方式你可以很方便的找到每一个index参数的值。

```
<body>
<p>查找最大的数。</p>
<p id="demo"></p>
<script>
x = findMax(1, 123, 500, 115, 44, 88);

function findMax() {
    var i, max = arguments[0];

    if(arguments.length < 2)return max;

    for (i = 0; i < arguments.length; i++) {
        if (arguments[i] > max) {
            max = arguments[i];
        }
    }
    return max;
}
document.getElementById("demo").innerHTML = x;
</script>
</body>
```

创建一个函数用来统计所有数值的和：

```
<body>
<p>计算所有参数之和：</p>
<p id="demo"></p>
<script>
function sumAll() {
    var i, sum = 0;
    for(i = 0; i < arguments.length; i++) {
        sum += arguments[i];
    }
    return sum;
} 
document.getElementById("demo").innerHTML =
	sumAll(1, 123, 500, 115, 44, 88);
</script>
</body>
```

**通过值传递参数**

avaScript函数传值只是将参数的值传入函数，函数会另外配置内存保存参数值，所以并不会改变原参数的值。

```
var x = 1;
// 通过值传递参数
function myFunction(x) {
    x++; //修改参数x的值，将不会修改在函数外定义的变量 x
    console.log(x);
}
myFunction(x); // 2
console.log(x); // 1
```

**通过对象传递参数**

在JavaScript中，可以引用对象的值。因此我们在函数内部修改对象的属性就会修改其初始的值。修改对象属性可作用于函数外部（全局变量）。

```
var obj = {x:1};
// 通过对象传递参数
function myFunction(obj) {
    obj.x++; //修改参数对象obj.x的值，函数外定义的obj也将会被修改
    console.log(obj.x);
}
myFunction(obj); // 2
console.log(obj.x); // 2
```

## JavaScript 函数调用

JavaScript 函数有 4 种调用方式。每种方式的不同在于 this 的初始化。一般而言，在Javascript中，this指向函数执行时的当前对象。注意 this 是保留关键字，你不能修改 this 的值。


**调用 JavaScript 函数**

```
<body>
<p>
全局函数 (myFunction) 返回参数参数相乘的结果：
</p>
<p id="demo"></p>
<script>
function myFunction(a, b) {
	return a * b;
}
document.getElementById("demo").innerHTML = myFunction(10, 2); 
</script>
</body>
```

以上函数不属于任何对象。但是在 JavaScript 中它始终是默认的全局对象。在 HTML 中默认的全局对象是 HTML 页面本身，所以函数是属于 HTML 页面。在浏览器中的页面对象是浏览器窗口(window 对象)。以上函数会自动变为 window 对象的函数。

myFunction() 和 window.myFunction() 是一样的：

```
<body>
<p>全局函数 myFunction() 会自动成为 window 对象的方法。</p>
<p>myFunction() 类似于 window.myFunction()。</p>
<p id="demo"></p>
<script>
function myFunction(a, b) {
	return a * b;
}
document.getElementById("demo").innerHTML = window.myFunction(10, 2); 
</script>
</body>
```

**全局对象**

当函数没有被自身的对象调用时， this 的值就会变成全局对象。在 web 浏览器中全局对象是浏览器窗口（window 对象）。

下面的实例返回 this 的值是 window 对象:

```
<body>
<p>在 HTML 中 <b>this</b> 的值, 在全局函数是一个 window 对象。</p>
<p id="demo"></p>
<script>
function myFunction() {
	return this;
}
document.getElementById("demo").innerHTML = myFunction(); 
</script>
</body>
```

函数作为全局对象调用，会使 this 的值成为全局对象。使用 window 对象作为一个变量容易造成程序崩溃。

**函数作为方法调用**

在 JavaScript 中你可以将函数定义为对象的方法。以下实例创建了一个对象 (myObject), 对象有两个属性 (firstName 和 lastName), 及一个方法 (fullName):

```
<body>
<p>myObject.fullName() 返回 John Doe:</p>
<p id="demo"></p>
<script>
var myObject = {
    firstName:"John",
    lastName: "Doe",
    fullName: function() {
		return this.firstName + " " + this.lastName;
    }
}
document.getElementById("demo").innerHTML = myObject.fullName(); 
</script>
</body>
```

fullName 方法是一个函数。函数属于对象。 myObject 是函数的所有者。this对象，拥有 JavaScript 代码。实例中 this 的值为 myObject 对象。函数作为对象方法调用，会使得 this 的值成为对象本身。


**使用构造函数调用函数**

如果函数调用前使用了 new 关键字, 则是调用了构造函数。构造函数的调用会创建一个新的对象。新对象会继承构造函数的属性和方法。构造函数中 this 关键字没有任何的值。this 的值在函数调用时实例化对象(new object)时创建。

```
<body>
<p>该实例中， myFunction 是函数构造函数:</p>
<p id="demo"></p>
<script>
function myFunction(arg1, arg2) {
    this.firstName = arg1;
    this.lastName  = arg2;
}
var x = new myFunction("John","Doe")
document.getElementById("demo").innerHTML = x.firstName; 
</script>
</body>
```

**作为函数方法调用函数**

在 JavaScript 中, 函数是对象。JavaScript 函数有它的属性和方法。call() 和 apply() 是预定义的函数方法。 两个方法可用于调用函数，两个方法的第一个参数必须是对象本身。

```
function myFunction(a, b) {
    return a * b;
}
myFunction.call(myObject, 10, 2);      // 返回 20
```

```
function myFunction(a, b) {
    return a * b;
}
myArray = [10,2];
myFunction.apply(myObject, myArray);   // 返回 20
```

两个方法都使用了对象本身作为第一个参数。 两者的区别在于第二个参数： apply传入的是一个参数数组，也就是将多个参数组合成为一个数组传入，而call则作为call的参数传入（从第二个参数开始）。

在 JavaScript 严格模式(strict mode)下, 在调用函数时第一个参数会成为 this 的值， 即使该参数不是一个对象。在 JavaScript 非严格模式(non-strict mode)下, 如果第一个参数的值是 null 或 undefined, 它将使用全局对象替代。通过 call() 或 apply() 方法你可以设置 this 的值, 且作为已存在对象的新方法调用。

## JavaScript 闭包

在web页面中全局变量属于 window 对象。全局变量可应用于页面上的所有脚本。局部变量只能用于定义它函数内部。对于其他的函数或脚本代码是不可用的。全局和局部变量即便名称相同，它们也是两个不同的变量。修改其中一个，不会影响另一个的值。变量声明是如果不使用 var 关键字，那么它就是一个全局变量，即便它在函数内定义。

**变量生命周期**

全局变量的作用域是全局性的，即在整个JavaScript程序中，全局变量处处都在。而在函数内部声明的变量，只在函数内部起作用。这些变量是局部变量，作用域是局部性的；函数的参数也是局部性的，只在函数内部起作用。

```
<body>

<p>全局变量计数。</p>
<button type="button" onclick="myFunction()">计数!</button>
<p id="demo">0</p>
<script>
var counter = 0;
function add() {
    return counter += 1;
}
function myFunction(){
    document.getElementById("demo").innerHTML = add();
}
</script>

</body>
```

**JavaScript 内嵌函数**

所有函数都能访问全局变量。实际上，在 JavaScript 中，所有函数都能访问它们上一层的作用域。JavaScript 支持嵌套函数。嵌套函数可以访问上一层的函数变量。下面实例中，内嵌函数 plus() 可以访问父函数的 counter 变量：

```
<body>

<p>局部变量计数。</p>
<p id="demo">0</p>
<script>
document.getElementById("demo").innerHTML = add();
function add() {
	var counter = 0;
    function plus() {counter += 1;}
    plus();    
    return counter; 
}
</script>

</body>
```

**JavaScript闭包**

闭包是可访问上一层函数作用域里变量的函数，即便上一层函数已经关闭。闭包（closure）是Javascript语言的一个难点，也是它的特色，很多高级应用都要依靠闭包实现。

由于在Javascript语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成"定义在一个函数内部的函数"。所以，在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

```
<body>
<p>局部变量计数。</p>
<button type="button" onclick="myFunction()">计数!</button>
<p id="demo">0</p>
<script>
var add = (function () {
    var counter = 0;
    return function () {return counter += 1;}
})();
function myFunction(){
    document.getElementById("demo").innerHTML = add();
}
</script>
</body>
```

变量 add 指定了函数自我调用的返回字值。自我调用函数只执行一次。设置计数器为 0。并返回函数表达式。add变量可以作为一个函数使用。特别之处在于它可以访问函数上一层作用域的计数器。这个叫作 JavaScript 闭包。它使得函数拥有私有变量变成可能。计数器受匿名函数的作用域保护，只能通过 add 方法修改。


**闭包的用途**

闭包可以用在许多地方。它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。

怎么来理解这句话呢？请看下面的代码：

```
function f1(){
　　　　var n=999;
　　　　nAdd=function(){n+=1}
　　　　function f2(){
　　　　　　alert(n);
　　　　}
　　　　return f2;
　　}
　　var result=f1();
　　result(); // 999
　　nAdd();
　　result(); // 1000
```

在这段代码中，result实际上就是闭包f2函数。它一共运行了两次，第一次的值是999，第二次的值是1000。这证明了，函数f1中的局部变量n一直保存在内存中，并没有在f1调用后被自动清除。

为什么会这样呢？原因就在于f1是f2的父函数，而f2被赋给了一个全局变量，这导致f2始终在内存中，而f2的存在依赖于f1，因此f1也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。

这段代码中另一个值得注意的地方，就是"nAdd=function(){n+=1}"这一行，首先在nAdd前面没有使用var关键字，因此nAdd是一个全局变量，而不是局部变量。其次，nAdd的值是一个匿名函数（anonymous function），而这个匿名函数本身也是一个闭包，所以nAdd相当于是一个setter，可以在函数外部对函数内部的局部变量进行操作。

**使用闭包的注意点**

1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

2）闭包会在父函数外部改变父函数内部变量的值。因此，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

## JavaScript HTML DOM(文档对象模型)

通过 HTML DOM，可访问 JavaScript HTML 文档的所有元素。当网页被加载时，浏览器会创建页面的文档对象模型（Document Object Model）。HTML DOM 定义了用于 HTML 的一系列标准的对象，以及访问和处理 HTML 文档的标准方法。通过 DOM，你可以访问所有的 HTML 元素，连同它们所包含的文本和属性。

HTML DOM 独立于平台和编程语言。它可被任何编程语言诸如 Java、JavaScript 和 VBScript 使用。HTML DOM 模型被构造为对象的树。

HTML DOM 模型被构造为对象的树：

![HTML DOM树.png](https://upload-images.jianshu.io/upload_images/4164292-01defdc1b7719238.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过可编程的对象模型，JavaScript 获得了足够的能力来创建动态的 HTML。包括如下几方面：

* JavaScript 能够改变页面中的所有 HTML 元素；
* JavaScript 能够改变页面中的所有 HTML 属性；
* JavaScript 能够改变页面中的所有 CSS 样式；
* JavaScript 能够对页面中的所有事件做出反应。

**查找 HTML 元素**

通常，通过 JavaScript，您需要操作 HTML 元素。为了做到这件事情，您必须首先找到该元素。有三种方法来做这件事：

* (1)通过 id 找到 HTML 元素。在 DOM 中查找 HTML 元素的最简单的方法，是通过使用元素的 id。如果找到该元素，则该方法将以对象（在 x 中）的形式返回该元素。如果未找到该元素，则 x 将包含 null。

```
<body>
<p id="intro">你好世界!</p>
<p>该实例展示了 <b>getElementById</b> 方法!</p>
<script>
x=document.getElementById("intro");
document.write("<p>文本来自 id 为 intro 段落: " + x.innerHTML + "</p>");
</script>
</body>
```

* (2)通过标签名找到 HTML 元素。下面例子查找 id="main" 的元素，然后查找 id="main" 元素中的所有 <p> 元素：

```
<body>
<p>你好世界!</p>
<div id="main">
<p> DOM 是非常有用的。</p>
<p>该实例展示了  <b>getElementsByTagName</b> 方法</p>
</div>
<script>
var x=document.getElementById("main");
var y=x.getElementsByTagName("p");
document.write('id="main"元素中的第一个段落为：' + y[1].innerHTML);
</script>
</body>
```
* (3)通过类名找到 HTML 元素。下面例子通过 getElementsByClassName 函数来查找 class="intro" 的元素：

```
<body>

<p class="intro">你好世界!</p>
<p>该实例展示了 <b>getElementsByClassName</b> 方法!</p>
<script>
x=document.getElementsByClassName("intro");
document.write("<p>文本来自 class 为 intro 段落: " + x[0].innerHTML + "</p>");
</script>
<p><b>注意：</b>Internet Explorer 8 及更早 IE 版本不支持 getElementsByClassName() 方法。</p>
</body>
```








