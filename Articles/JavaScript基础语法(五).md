## JavaScript Array（数组）对象

[JavaScript Array 对象](https://www.w3cschool.cn/jsref/jsref-obj-array.html)

数组对象的作用是：使用单独的变量名来存储一系列的值。创建数组有以下三种方式：

* (1)常规方式

```
var mycars = new Array();
mycars[0] = "Saab";
mycars[1] = "Volvo";
mycars[2] = "BMW";
```

* (2)简洁方式：

```
var myCars=new Array("Saab","Volvo","BMW");
```

* (3)字面的方式

```
var myCars=["Saab","Volvo","BMW"];
```

**访问数组**：通过指定数组名以及索引号码，你可以访问某个特定的元素。

访问myCars数组的第一个值。

```
var name = myCars[0];
```

以下实例修改了数组myCars的第一个元素：

```
myCars[0]="Opel";
```

在一个数组中你可以有不同的对象。所有的JavaScript变量都是对象。数组元素是对象。函数是对象。因此你可以在数组中有不同的变量类型。你可以在一个数组中包含对象元素、函数、数组。

```
myArray[0]=Date.now;
myArray[1]=myFunction;
myArray[2]=myCars; 
```

**数组方法和属性**：使用数组对象预定义属性和方法。

```
var x=myCars.length             // the number of elements in myCars
var y=myCars.indexOf("Volvo")   // the index position of "Volvo"
```

**合并两个数组**

```
var hege = ["Cecilie", "Lone"];
var stale = ["Emil", "Tobias", "Linus"];
var children = hege.concat(stale);
```

合并三个数组：

```
var parents = ["Jani", "Tove"];
var brothers = ["Stale", "Kai Jim", "Borge"];
var children = ["Cecilie", "Lone"];
var family = parents.concat(brothers, children);
```

**删除数组的最后一个元素**

```
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.pop();
```

**在数组末尾添加一个新元素**

```
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.push("Kiwi")
```

**数组反转排序**

```
fruits.reverse();
```

**删除数组第一个元素**

```
fruits.shift();
```

**截取下标1到2的元素**

```
var fruits = ["Banana", "Orange", "Lemon", "Apple", "Mango"];
var citrus = fruits.slice(1,3);
```

**数组元素按照字母升序排列**

```
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.sort();
```

**数组元素按照数字字母升序**

```
var points = [40,100,1,5,25,10];
points.sort(function(a,b){return a-b});
```
  
## JavaScript Boolean（布尔）对象

创建 Boolean 对象。下面的代码定义了一个名为 myBoolean 的布尔对象：

```
var myBoolean=new Boolean();
```
如果布尔对象无初始值或者其值为:0、-0、null、""、false、undefined、NaN，那么对象的值为false，否则，其值为true。

## JavaScript Math（算数）对象

Math（算数）对象的作用是：执行常见的算数任务。

**Math 对象**：Math 对象提供多种算数值类型和函数。无需在使用这个对象之前对它进行定义。Math 对象不能使用 new 关键字创建对象实例。直接用 “对象名.成员”的格式来访问其属性或者方法。

**算数值**

JavaScript 提供 8 种可被 Math 对象访问的算数值：

```
 Math.E
 Math.PI
 Math.SQRT2
 Math.SQRT1_2
 Math.LN2
 Math.LN10
 Math.LOG2E
 Math.LOG10E
```

**算数方法**

下面的例子使用了 Math 对象的 round 方法对一个数进行四舍五入:

```
document.write(Math.round(4.7));
```

下面的例子使用了 Math 对象的 random() 方法来返回一个介于 0 和 1 之间的随机数：

```
document.write(Math.random());
```

## JavaScript RegExp 对象

RegExp：是正则表达式（regular expression）的简写。

[JavaScript RegExp 对象的参考手册](https://www.w3cschool.cn/jsref/jsref-obj-regexp.html)

**RegExp 修饰符**

修饰符用于执行不区分大小写和全文的搜索。

* i - 修饰符是用来执行不区分大小写的匹配。
* g - 修饰符是用于执行全文的搜索（而不是在找到第一个就停止查找,而是找到所有的匹配）。

**test()**

The test()方法搜索字符串指定的值，根据结果并返回真或假。下面的示例是从字符串中搜索字符 "e" ：

```
var patt1=new RegExp("e"); 
document.write(patt1.test("The best things in life are free"));
```

**exec()**

exec() 方法检索字符串中的指定值。返回值是被找到的值。如果没有发现匹配，则返回 null。

下面的示例是从字符串中搜索字符 "e" ：

```
var patt1=new RegExp("e"); 
document.write(patt1.exec("The best things in life are free"));
```

## JavaScript Window 对象

Window 对象表示浏览器中打开的窗口。在客户端 JavaScript 中，Window 对象是全局对象，所有的表达式都在当前的环境中计算。

[JavaScript Window 对象的参考手册](https://www.w3cschool.cn/jsref/obj-window.html)

**Window 子对象**

Window的子对象主要有如下几个：

* JavaScript document 对象
* JavaScript frames 对象
* JavaScript history 对象
* JavaScript location 对象
* JavaScript navigator 对象
* JavaScript screen 对象

**Window 尺寸**

对于Internet Explorer、Chrome、Firefox、Opera 以及 Safari：

* window.innerHeight - 浏览器窗口的内部高度
* window.innerWidth - 浏览器窗口的内部宽度

对于 Internet Explorer 8、7、6、5：

* document.documentElement.clientHeight
* document.documentElement.clientWidth

或者

* document.body.clientHeight
* document.body.clientWidth

```
var w=window.innerWidth || document.documentElement.clientWidth || document.body.clientWidth;
var h=window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight;
```

## JavaScripy execCommand函数

execCommand方法是执行一个对当前文档，当前选择或者给出范围的命令。处理Html数据时常用如下格式：document.execCommand(sCommand[,交互方式, 动态参数]) ，其中：sCommand为指令参数（如下例中的”2D-Position”），交互方式参数如果是true的话将显示对话框，如果为false的话，则不显示对话框（下例中的”false”即表示不显示对话框），动态参数一般为一可用值或属性值（如下例中的”true”）。

```
document.execCommand("2D-Position","false","true");
```

相关指令参数及意义：

```
2D-Position 允许通过拖曳移动绝对定位的对象。
AbsolutePosition 设定元素的 position 属性为“absolute”(绝对)。
BackColor 设置或获取当前选中区的背景颜色。
BlockDirLTR 目前尚未支持。
BlockDirRTL 目前尚未支持。
Bold 切换当前选中区的粗体显示与否。
BrowseMode 目前尚未支持。
Copy 将当前选中区复制到剪贴板。
CreateBookmark 创建一个书签锚或获取当前选中区或插入点的书签锚的名称。
CreateLink 在当前选中区上插入超级链接，或显示一个对话框允许用户指定要为当前选中区插入的超级链接的 URL。
Cut 将当前选中区复制到剪贴板并删除之。
Delete 删除当前选中区。
DirLTR 目前尚未支持。
DirRTL 目前尚未支持。
EditMode 目前尚未支持。
FontName 设置或获取当前选中区的字体。
FontSize 设置或获取当前选中区的字体大小。
ForeColor 设置或获取当前选中区的前景(文本)颜色。
FormatBlock 设置当前块格式化标签。
Indent 增加选中文本的缩进。
InlineDirLTR 目前尚未支持。
InlineDirRTL 目前尚未支持。
InsertButton 用按钮控件覆盖当前选中区。
InsertFieldset 用方框覆盖当前选中区。
InsertHorizontalRule 用水平线覆盖当前选中区。
InsertIFrame 用内嵌框架覆盖当前选中区。
InsertImage 用图像覆盖当前选中区。
InsertInputButton 用按钮控件覆盖当前选中区。
InsertInputCheckbox 用复选框控件覆盖当前选中区。
InsertInputFileUpload 用文件上载控件覆盖当前选中区。
InsertInputHidden 插入隐藏控件覆盖当前选中区。
InsertInputImage 用图像控件覆盖当前选中区。
InsertInputPassword 用密码控件覆盖当前选中区。
InsertInputRadio 用单选钮控件覆盖当前选中区。
InsertInputReset 用重置控件覆盖当前选中区。
InsertInputSubmit 用提交控件覆盖当前选中区。
InsertInputText 用文本控件覆盖当前选中区。
InsertMarquee 用空字幕覆盖当前选中区。
InsertOrderedList 切换当前选中区是编号列表还是常规格式化块。
InsertParagraph 用换行覆盖当前选中区。
InsertSelectDropdown 用下拉框控件覆盖当前选中区。
InsertSelectListbox 用列表框控件覆盖当前选中区。
InsertTextArea 用多行文本输入控件覆盖当前选中区。
InsertUnorderedList 切换当前选中区是项目符号列表还是常规格式化块。
Italic 切换当前选中区斜体显示与否。
JustifyCenter 将当前选中区在所在格式化块置中。
JustifyFull 目前尚未支持。
JustifyLeft 将当前选中区所在格式化块左对齐。
JustifyNone 目前尚未支持。
JustifyRight 将当前选中区所在格式化块右对齐。
LiveResize 迫使 MSHTML 编辑器在缩放或移动过程中持续更新元素外观，而不是只在移动或缩放完成后更新。
MultipleSelection 允许当用户按住 Shift 或 Ctrl 键时一次选中多于一个站点可选元素。
Open 目前尚未支持。
Outdent 减少选中区所在格式化块的缩进。
OverWrite 切换文本状态的插入和覆盖。
Paste 用剪贴板内容覆盖当前选中区。
PlayImage 目前尚未支持。
Print 打开打印对话框以便用户可以打印当前页。
Redo 目前尚未支持。
Refresh 刷新当前文档。
RemoveFormat 从当前选中区中删除格式化标签。
RemoveParaFormat 目前尚未支持。
SaveAs 将当前 Web 页面保存为文件。
SelectAll 选中整个文档。
SizeToControl 目前尚未支持。
SizeToControlHeight 目前尚未支持。
SizeToControlWidth 目前尚未支持。
Stop 目前尚未支持。
StopImage 目前尚未支持。
StrikeThrough 目前尚未支持。
Subscript 目前尚未支持。
Superscript 目前尚未支持。
UnBookmark 从当前选中区中删除全部书签。
Underline 切换当前选中区的下划线显示与否。
Undo 目前尚未支持。
Unlink 从当前选中区中删除全部超级链接。
Unselect 清除当前选中区的选中状态。
```

## JavaScript弹框

可以在 JavaScript 中创建三种消息框：警告框、确认框、提示框。

* 警告框。警告框经常用于确保用户可以得到某些信息。当警告框出现后，用户需要点击确定按钮才能继续进行操作。

```
window.alert("sometext");
```

window.alert() 方法可以不带上window对象，直接使用alert()方法。

* 确认框。确认框通常用于验证是否接受用户操作。当确认卡弹出时，用户可以点击 "确认" 或者 "取消" 来确定用户操作。当你点击 "确认", 确认框返回 true， 如果点击 "取消", 确认框返回 false。

```
window.confirm("sometext");
```

* 提示框。提示框经常用于提示用户在进入页面前输入某个值。当提示框出现后，用户需要输入某个值，然后点击确认或取消按钮才能继续操纵。如果用户点击确认，那么返回值为输入的值。如果用户点击取消，那么返回值为 null。

```
window.prompt("sometext","defaultvalue");
```
例如：

```
<body>

<p>点击按钮查看输入的对话框。</p>
<button onclick="myFunction()">点我</button>
<p id="demo"></p>
<script>
function myFunction(){
	var x;
	var person=prompt("请输入你的名字","Harry Potter");
	if (person!=null && person!=""){
	    x="你好 " + person + "！今天感觉如何？";
	    document.getElementById("demo").innerHTML=x;
	}
}
</script>

</body>
</html>
```

## JavaScript 计时事件

通过使用 JavaScript，我们有能力做到在一个设定的时间间隔之后来执行代码，而不是在函数被调用后立即执行。我们称之为计时事件。在 JavaScritp 中使用计时事件是很容易的，两个关键方法是:

* setInterval() - 间隔指定的毫秒数不停地执行指定的代码

```
setInterval(function(){alert("Hello")},3000);
```
* setTimeout() - 暂停指定的毫秒数后执行指定的代码

停止方法如下：

* clearInterval() 方法用于停止 setInterval() 方法执行的函数代码。
* clearTimeout() 方法用于停止执行setTimeout()方法的函数代码。

```
var myVar; 

function myFunction() 
{ 
	myVar=setTimeout(function(){alert("Hello")},3000); 
} 

function myStopFunction() 
{ 
	clearTimeout(myVar); 
}
```

## JavaScript Cookies

Cookies 的作用就是用于解决 "如何记录客户端的用户信息":

* 当用户访问 web 页面时，他的名字可以记录在 cookie 中。
* 在用户下一次访问该页面时，可以在 cookie 中读取用户访问记录。

Cookies 以名/值对形式存储。当浏览器从服务器上请求 web 页面时， 属于该页面的 cookies 会被添加到该请求中。服务端通过这种方式来获取用户的信息。

**创建Cookie**

JavaScript 中，创建 cookie 如下所示：  

```
document.cookie="username=John Doe";
```

还可以为 cookie 添加一个过期时间（以 UTC 或 GMT 时间）。默认情况下，cookie 在浏览器关闭时删除：

```
document.cookie="username=John Doe; expires=Thu, 18 Dec 2013 12:00:00 GMT";
```

可以使用 path 参数告诉浏览器 cookie 的路径。默认情况下，cookie 属于当前页面:

```
document.cookie="username=John Doe; expires=Thu, 18 Dec 2013 12:00:00 GMT; path=/";
```

**读取 Cookie**

在 JavaScript 中, 可以使用以下代码来读取 cookies：

```
var x = document.cookie;
```

document.cookie 将以字符串的方式返回所有的 cookies，类型格式： cookie1=value; cookie2=value; cookie3=value;

**使用 JavaScript 修改 Cookie**

```
document.cookie="username=John Smith; expires=Thu, 18 Dec 2013 12:00:00 GMT; path=/";
```

旧的 cookie 将被覆盖。


**使用 JavaScript 删除 Cookie**

删除 cookie 非常简单。您只需要设置 expires 参数为以前的时间即可，如下所示，设置为 Thu, 01 Jan 1970 00:00:00 GMT:

```
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 GMT";
```

**设置 cookie 值的函数**

```
function setCookie(cname,cvalue,exdays)        
{        
	var d = new Date();        
	d.setTime(d.getTime()+(exdays*24*60*60*1000));        
	var expires = "expires="+d.toGMTString();        
	document.cookie = cname + "=" + cvalue + "; " + expires;        
}
```

以上的函数参数中，cookie 的名称为 cname，cookie 的值为 cvalue，并设置了 cookie 的过期时间 expires。该函数设置了 cookie 名、cookie 值、cookie过期时间。

**获取 cookie 值的函数**

创建一个函数用户返回指定 cookie 的值：

```
function getCookie(cname)        
{        
	var name = cname + "=";        
	var ca = document.cookie.split(';');        
	for(var i=0; i<ca.length; i++)        
	  {        
	  var c = ca[i].trim();        
	  if (c.indexOf(name)==0) return c.substring(name.length,c.length);        
	  }        
	return "";        
}
```

cookie 名的参数为 cname。创建一个文本变量用于检索指定 cookie :cname + "="。使用分号来分割 document.cookie 字符串，并将分割后的字符串数组赋值给 ca (ca = document.cookie.split(';'))。循环 ca 数组 (i=0;i<ca.length;i++)，然后读取数组中的每个值，并去除前后空格 (c=ca[i].trim())。如果找到 cookie(c.indexOf(name) == 0)，返回 cookie 的值 (c.substring(name.length,c.length)。如果没有找到 cookie, 返回 ""。

**检测 cookie 值的函数**

创建一个检测 cookie 是否创建的函数。

```
function checkCookie()        
{        
var username=getCookie("username");        
if (username!="")        
  {   
  //如果设置了 cookie，将显示一个问候信息。
  alert("Welcome again " + username);        
  }        
else        
  {        
  username = prompt("Please enter your name:","");        
  if (username!="" && username!=null)        
    {   
    //如果没有设置 cookie，将会显示一个弹窗用于询问访问者的名字，并调用 setCookie 函数将访问者的名字存储 365 天
    setCookie("username",username,365);        
    }        
  }        
}
```















