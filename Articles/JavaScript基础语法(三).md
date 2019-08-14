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

## HTML DOM 改变 HTML 内容

HTML DOM 允许 JavaScript 改变 HTML 元素的内容。

**改变 HTML 输出流**

在 JavaScript 中，document.write() 可用于直接向 HTML 输出流写内容。需要特别注意的是，绝对不要在文档加载完成之后使用 document.write()。这会覆盖该文档。

```
<body>
<script>
document.write(Date());
</script>
</body>
```

**改变 HTML 内容**

改变 HTML 元素的内容的语法如下：

```
document.getElementById(id).innerHTML=new HTML
```

示例：

```
<body>

<p id="p1">Hello World!</p>
<script>
document.getElementById("p1").innerHTML="新文本!";
</script>
<p>以上段落通过脚本修改文本。</p>

</body>
```

**改变 HTML 属性**

如需改变 HTML 元素的属性，请使用这个语法：

```
document.getElementById(id).attribute=new value
```

示例：

```
<body>

<img id="image" src="smiley.gif">

<script>
document.getElementById("image").src="landscape.jpg";
</script>

</body>
```

## HTML DOM 改变 CSS

HTML DOM 允许 JavaScript 改变 HTML 元素的样式。

**改变 HTML 样式**

如需改变 HTML 元素的样式，请使用这个语法：  

```
document.getElementById(id).style.property=new style
```

示例1：

```
<body> 
<p id="p2">Hello World!</p> 
<script> 
document.getElementById("p2").style.color="blue"; 
</script> 
<p>The paragraph above was changed by a script.</p> 
</body> 
```

示例2：本例表示的是 当用户点击按钮时，改变了 id="id1" 的 HTML 元素的样式

```
<body> 
<h1 id="id1">My Heading 1</h1> 
<button type="button" 
onclick="document.getElementById('id1').style.color='red'"> 
Click Me!</button> 
</body> 
```

## HTML DOM 事件

HTML DOM 使 JavaScript 有能力对 HTML 事件做出反应。我们可以在事件发生时执行 JavaScript，比如当用户在 HTML 元素上点击时。如需在用户点击某个元素时执行代码，请向一个 HTML 事件属性添加 JavaScript 代码： 

```
onclick=JavaScript
```

HTML 事件包括以下这些：

* 当用户点击鼠标时;
* 当网页已加载时;
* 当图像已加载时;
* 当鼠标移动到元素上时;
* 当输入字段被改变时;
* 当提交 HTML 表单时;
* 当用户触发按键时。

```
<body>
<h1 onclick="this.innerHTML='Ooops!'">点击文本!</h1>
</body>
```

也可写成这种形式：

```
<!DOCTYPE html> 
<html> 
<head> 
<script> 
function changetext(id) 
{ 
   id.innerHTML="Ooops!"; 
} 
</script> 
</head> 

<body> 
<h1 onclick="changetext(this)">Click on this text!</h1> 
</body> 
</html>
```

**HTML 事件属性**

如需向 HTML 元素分配 事件，您可以使用事件属性。向 button 元素分配 onclick 事件：

```
<body>

<p>点击按钮执行 <em>displayDate()</em> 函数.</p>
<button onclick="displayDate()">点我</button>

<script>
function displayDate()
{
	document.getElementById("demo").innerHTML=Date();
}
</script>
<p id="demo"></p>

</body>
```

**使用 HTML DOM 来分配事件**

HTML DOM 允许您使用 JavaScript 来向 HTML 元素分配事件。向 button 元素分配 onclick 事件：

```
<body>

<p>点击按钮执行 <em>displayDate()</em> 函数.</p>
<button id="myBtn">点这里</button>

<script>
document.getElementById("myBtn").onclick=function(){displayDate()};
function displayDate()
{
	document.getElementById("demo").innerHTML=Date();
}
</script>

<p id="demo"></p>

</body>
```

在上面的例子中，名为 displayDate 的函数被分配给 id=myBtn" 的 HTML 元素。按钮点击时Javascript函数将会被执行。

**onload 和 onunload 事件**

onload 和 onunload 事件会在用户进入或离开页面时被触发。onload 事件可用于检测访问者的浏览器类型和浏览器版本，并基于这些信息来加载网页的正确版本。onload 和 onunload 事件可用于处理 cookie。

```
<body onload="checkCookies()">
<script>
function checkCookies(){
	if (navigator.cookieEnabled==true){
		alert("Cookies 可用")
	}
	else{
		alert("Cookies 不可用")
	}
}
</script>
<p>弹窗-提示浏览器cookie是否可用。</p>
</body>
```

**onchange 事件**

onchange 事件常结合对输入字段的验证来使用。下面是一个如何使用 onchange 的例子。当用户改变输入字段的内容时，会调用 upperCase() 函数。

```
<head>
<script>
function myFunction(){
	var x=document.getElementById("fname");
	x.value=x.value.toUpperCase();
}
</script>
</head>
<body>

输入你的名字: <input type="text" id="fname" onchange="myFunction()">
<p>当你离开输入框后，函数将被触发，将小写字母转为大写字母。</p>

</body>
```

**onmouseover 和 onmouseout 事件**

onmouseover 和 onmouseout 事件可用于在用户的鼠标移至 HTML 元素上方或移出元素时触发函数。

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>W3Cschool教程(w3cschool.cn)</title>
</head>
<body>

<div onmouseover="mOver(this)" onmouseout="mOut(this)" style="background-color:#D94A38;width:120px;height:20px;padding:40px;">Mouse Over Me</div>
<script>
function mOver(obj){
	obj.innerHTML="Thank You"
}
function mOut(obj){
	obj.innerHTML="Mouse Over Me"
}
</script>

</body>
</html>
```

**onmousedown、onmouseup 以及 onclick 事件**

onmousedown, onmouseup 以及 onclick 构成了鼠标点击事件的所有部分。首先当点击鼠标按钮时，会触发 onmousedown 事件，当释放鼠标按钮时，会触发 onmouseup 事件，最后，当完成鼠标点击时，会触发 onclick 事件。

```
<body>

<div onmousedown="mDown(this)" onmouseup="mUp(this)" style="background-color:#D94A38;width:90px;height:20px;padding:40px;">Click Me</div>

<script>
function mDown(obj)
{
    obj.style.backgroundColor="#1ec5e5";
    obj.innerHTML="Release Me"
}

function mUp(obj)
{
    obj.style.backgroundColor="#D94A38";
    obj.innerHTML="Thank You"
}
</script>

</body>
```

**onfocus 事件**

当输入字段获得焦点时，触发某事件。

下面的例子，当输入字段获得焦点时，改变其背景色：

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>W3Cschool教程(w3cschool.cn)</title>
<script>
function myFunction(x){
	x.style.background="yellow";
}
</script>
</head>
<body>

输入你的名字: <input type="text" onfocus="myFunction(this)">
<p>当输入框获取焦点时，修改背景色（background-color属性） 将被触发。</p>

</body>
</html>
```

## HTML DOM EventListener

**addEventListener() 方法**

addEventListener() 方法用于向指定元素添加事件句柄。addEventListener() 方法添加的事件句柄不会覆盖已存在的事件句柄。可以向一个元素添加多个事件句柄。可以向同个元素添加多个同类型的事件句柄，如：两个 "click" 事件。可以向任何 DOM 对象添加事件监听，不仅仅是 HTML 元素。如： window 对象。你可以使用 removeEventListener() 方法来移除事件的监听。

当你使用 addEventListener() 方法时, JavaScript 从 HTML 标记中分离开来，可读性更强， 在没有控制HTML标记时也可以添加事件监听。

addEventListener()方法语法如下：

```
element.addEventListener(event, function, useCapture);
```

第一个参数是事件的类型 (如 "click" 或 "mousedown")。第二个参数是事件触发后调用的函数。第三个参数是个布尔值用于描述事件是冒泡还是捕获。该参数是可选的。需要注意的是，不要使用 "on" 前缀。 例如，使用 "click" ,而不是使用 "onclick"。

```
<body>

<p>该实例使用 addEventListener() 方法在按钮中添加点击事件。 </p>
<button id="myBtn">点我</button>
<p id="demo"></p>
<script>
document.getElementById("myBtn").addEventListener("click", displayDate);
function displayDate() {
    document.getElementById("demo").innerHTML = Date();
}
</script>

</body>
```

**向原元素添加事件句柄**

```
<body>

<p>该实例使用 addEventListener() 方法在用户点击按钮时执行函数。</p>
<button id="myBtn">点我</button>
<script>
document.getElementById("myBtn").addEventListener("click", myFunction);
function myFunction() {
    alert ("Hello World!");
}
</script>

</body>
```

**向同一个元素中添加多个事件句柄**

addEventListener() 方法允许向同个元素添加多个事件，且不会覆盖已存在的事件：

```
<body>

<p>该实例使用 addEventListener() 方法向同个按钮中添加两个点击事件。</p>
<button id="myBtn">点我</button>
<script>
var x = document.getElementById("myBtn");
x.addEventListener("click", myFunction);
x.addEventListener("click", someOtherFunction);
function myFunction() {
    alert ("Hello World!")
}
function someOtherFunction() {
    alert ("函数已执行!")
}
</script>

</body>
</html>
```

**向 Window 对象添加事件句柄**

addEventListener() 方法允许你在 HTML DOM 对象添加事件监听， HTML DOM 对象如： HTML 元素, HTML 文档, window 对象。或者其他支出的事件对象如: xmlHttpRequest 对象。

```
<body>

<p>实例在window对象中使用 addEventListener() 方法。</p>
<p>尝试重置浏览器的窗口触发 "resize" 事件句柄。</p>
<p id="demo"></p>
<script>
window.addEventListener("resize", function(){
    document.getElementById("demo").innerHTML = Math.random();
});
</script>

</body>
```

**传递参数**

当传递参数值时，使用"匿名函数"调用带参数的函数：

```
<body>

<p>实例演示了在使用 addEventListener() 方法时如何传递参数。</p>
<p>点击按钮执行计算。</p>
<button id="myBtn">点我</button>
<p id="demo"></p>
<script>
var p1 = 5;
var p2 = 7;
document.getElementById("myBtn").addEventListener("click", function() {
    myFunction(p1, p2);
});
function myFunction(a, b) {
    var result = a * b;
    document.getElementById("demo").innerHTML = result;
}
</script>

</body>
```

**事件冒泡 VS 事件捕获**

事件传递有两种方式：冒泡与捕获。事件传递定义了元素事件触发的顺序。如果你将p元素插入到div元素中，用户点击p元素，哪个元素的“click”事件先被触发呢？

在冒泡中，内部元素的事件会先被触发，然后再触发外部元素，即：p元素的点击事件先触发，然后会触发div元素的点击事件。
	
在捕获中，外部元素的事件会先被触发，然后才会触发内部元素的事件，即：div元素的点击事件先触发，然后再触发p元素的点击事件。
	
**addEventListener() 方法可以指定 "useCapture" 参数来设置传递类型：**

```
addEventListener(event, function, useCapture);
```

**useCapture默认值为 false, 即冒泡传递，当值为 true 时, 事件使用捕获传递。**

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>W3Cschool教程(w3cschool.cn)</title>
<style>
div {
    background-color: coral;
    border: 1px solid;
    padding: 50px;
}
</style>
</head>
<body>

<p>实例演示了在添加不同事件监听时，冒泡与捕获的不同。</p>
<div id="myDiv">
	<p id="myP">点击段落，我是冒泡。</p>
</div><br>
<div id="myDiv2">
	<p id="myP2">点击段落，我是捕获。 </p>
</div>
<script>
document.getElementById("myP").addEventListener("click", function() {
    alert("你点击了 P 元素!");
}, false);
document.getElementById("myDiv").addEventListener("click", function() {
    alert(" 你点击了 DIV 元素 !");
}, false);
document.getElementById("myP2").addEventListener("click", function() {
    alert("你点击了 P2 元素!");
}, true);
document.getElementById("myDiv2").addEventListener("click", function() {
    alert("你点击了 DIV2 元素 !");
}, true);
</script>

</body>
</html>
```

**removeEventListener() 方法**

removeEventListener() 方法的作用是移除由 addEventListener() 方法添加的事件句柄。

示例如下：

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>W3Cschool教程(w3cschool.cn)</title>
</head>
<head>
<style>
#myDIV {
    background-color: coral;
    border: 1px solid;
    padding: 50px;
    color: white;
}
</style>
</head>
<body>

<div id="myDIV"> div 元素添加了 onmousemove 事件句柄，鼠标在桔红色的框内移动时会显示随机数。
  <p>点击按钮移除 DIV 的事件句柄。</p>
  <button onclick="removeHandler()" id="myBtn">点我</button>
</div>
<p id="demo"></p>
<script>
document.getElementById("myDIV").addEventListener("mousemove", myFunction);
function myFunction() {
    document.getElementById("demo").innerHTML = Math.random();
}
function removeHandler() {
    document.getElementById("myDIV").removeEventListener("mousemove", myFunction);
}
</script>

</body>
</html>
```

**需要注意的是**，IE 8 及更早 IE 版本，Opera 7.0及其更早版本不支持 addEventListener() 和 removeEventListener() 方法。但是，对于这类浏览器版本可以使用 detachEvent() 方法来移除事件句柄:

```
element.attachEvent(event, function);
element.detachEvent(event, function);
```

示例如下：

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>W3Cschool教程(w3cschool.cn)</title>
</head>
<body>

<p> Internet Explorer 8 及更早IE版本不支持addEventListener() 方法。</p>
<p>该实例演示了所有浏览器兼容的解决方法。</p>
<button id="myBtn">点我</button>
<script>
var x = document.getElementById("myBtn");
if (x.addEventListener) {
    x.addEventListener("click", myFunction);
} else if (x.attachEvent) {
    x.attachEvent("onclick", myFunction);
}
function myFunction() {
    alert("Hello World!");
}
</script>

</body>
</html>
```

## HTML DOM 元素

在文档对象模型 (DOM) 中，每个节点都是一个对象。DOM 节点有三个重要的属性，分别是：

* nodeName : 节点的名称
* nodeValue ：节点的值
* nodeType ：节点的类型

**创建新的 HTML 元素**

如需向 HTML DOM 添加新元素，您必须首先创建该元素（元素节点），然后向一个已存在的元素追加该元素。

示例如下：

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>W3Cschool教程(w3cschool.cn)</title>
</head>
<body>

<div id="div1">
<p id="p1">这是一个段落。</p>
<p id="p2">这是另一个段落。</p>
</div>
<script>
//创建新的<p> 元素
var para=document.createElement("p");
//如需向 <p> 元素添加文本，您必须首先创建文本节点。这段代码创建了一个文本节点
var node=document.createTextNode("这是一个新段落。");
//向 <p> 元素追加文本节点
para.appendChild(node);
var element=document.getElementById("div1");
//向一个已有的元素追加这个新元素
element.appendChild(para);
</script>

</body>
</html>
```

**删除已有的 HTML 元素**

以下代码是用来将已有的元素删除：

```
<div id="div1">
<p id="p1">This is a paragraph.</p>
<p id="p2">This is another paragraph.</p>
</div>
<script>
var parent=document.getElementById("div1");
var child=document.getElementById("p1");
//从父元素中删除子元素
parent.removeChild(child);
</script>
```

常用的解决方案：找到您希望删除的子元素，然后使用其 parentNode 属性来找到父元素：

```
var child=document.getElementById("p1");
child.parentNode.removeChild(child);
```
