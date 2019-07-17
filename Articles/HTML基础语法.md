# HTML基础语法

HTML是什么呢？HTML(Hyper Text Markup Language) 即超文本标记语言。HTML 不是一种编程语言，而是一种标记语言。标记语言是一套标记标签 (markup tag)。HTML 使用标记标签来描述网页。HTML 文档包含了HTML 标签及文本内容。HTML 文档也叫做 web 页面。

## HTML的结构

HTML的基本结构如下：

```
//<!DOCTYPE>是标准通用标记语言的文档类型声明，有助于在浏览器中正确地显示网页。doctype声明是不区分大小写的。
<!DOCTYPE html> 
<html> 
<body> 
<h1>我的第一个标题</h1> 
<p>我的第一个段落。</p> 
</body> 
</html>
```

## HTML标签

HTML 标记标签通常被称为 HTML 标签 (HTML tag)。HTML 标签是由尖括号包围的关键词，比如<html>。HTML 标签通常是成对出现的，比如<b>和</b>。开始和结束标签也被称为开放标签和闭合标签。
  
"HTML 标签" 和 "HTML 元素" 通常都是描述相同的意思。但是严格来讲，一个 HTML元素包含了开始标签与结束标签。

### HTML标题

HTML 标题（Heading）是通过h1-h6 元素来定义的。"h1" 表示主标题（the main heading），“h2 ”表示二级子标题（subheadings），“h3”表示三级子标题（sub-subheadings），“h4"、"h5"、"h6"依次递减字体的大小。
  
  
```
<h1>我的第一个标题一级标题</h1>
<h2 style="background-color: green">二级标题</h2>
<h3 style="background-color: gray">三级标题</h3>
//使用font-family（字体），color（颜色），和font-size（字体大小）属性来定义字体的样式，而不是使用<font>标签。
<h4 style="font-family: verdana;color: greenyellow;font-size: 20px">四级标题</h4>
//使用 text-align（文字对齐）属性指定文本的水平与垂直对齐方式。文本对齐属性 text-align取代了旧标签 <center>
<h5 style="text-align: center">五级标题</h5>
<h6>六级标题</h6>
```
  
### HTML段落

HTML 段落是通过标签<p>来定义的，P是英文paragraph段落的缩写，经常被用来创建一个段落。<p> 标签是用来定义段落的，浏览器会自动地在段落的前后添加空行。忘记使用结束标签会产生意想不到的结果和错误。在未来的 HTML 版本中，不允许省略结束标签。

```
<p style="background-color: gold">我的第一个段落</p>
<p>这是另一个段落</p>
```

### HTML链接

HTML 链接是通过标签<a>来定义的。a标签，也叫anchor（锚点）元素，既可以用来链接到外部地址实现页面跳转功能，也可以链接到当前页面的某部分实现内部导航功能。 在href属性中指定链接的地址。
  
```
<a href="http://www.w3cschool.cn">w3cschool官网</a>
```
### HTML 换行标签<br/>

HTML 空元素即为没有内容的 HTML 元素。HTML 空元素应该在开始标签中关闭。HTML 的一个空元素为 <br>（用于定义换行），<br> 元素就是没有关闭标签。HTML 空元素的关闭方法是在开始标签中添加斜杠，比如 <br />，HTML、XHTML 和 XML 都接受这种方式。

<br/>用于换行。

```
<br/>
```

hr 元素在 HTML 页面中创建水平线。

```
<hr>
```
### 图像标签 <img>

HTML 图像是通过标签<img>来定义的。使用img元素来为你的网站添加图片，使用src 属性指向一个图片的具体地址。img元素是自关闭元素，不需要结束标记。

```
<img border="1"
     src="imagesFile/RedPacketListImg.jpg"
     alt="Pulpit rock"
     width="100"
     height="60"
     align="middle">
```
### 强调标签
  
b 元素用来定义加粗 
  
i 元素用来定义斜体文本。
  
strong 元素或者 em 元素是为了强调而加粗显示。

```
<p>话说天下大势，<em>分久必合</em>，合久必分。<strong>周末七国分争</strong>，并入于秦。及秦灭之后，楚、汉分争，又并入于汉……</p>
<p>且说董卓字仲颖，<b>陇西临洮</b>人也，官拜<i>河东太守</i>，自来骄傲。当日怠慢了玄德，张飞性发，便欲杀之……</p>
```

### 格式化标签

|元素|作用|
|:---|:--|
|b元素|定义粗体文本|
|em元素|定义着重文字|
|i元素|定义斜体字|
|small元素|定义小号字|
|strong元素|定义加重语气|
|sub元素|定义下标字|
|sup元素|定义上标字|
|ins元素|定义插入字|
|del元素|定义删除字|

```
<!--上标标签<sup></sup>-->
<p>10<sup>5</sup></p>
<!--<sub>标签用来定义下标字-->
<p>H<sub>2</sub>0</p>
<!--<small>标签用来定义小号字-->
<p>small标签<small>用来定义</small>小号字</p>
<!--<p>big标签用来定义<big>大号字</big></p>-->
<!--ins标签用来定义插入文本-->
<p>ins标签用来定义<ins>插入字</ins></p>
<!--del标签用来定义删除文本-->
<p>del标签用来定义<del>删除字</del></p>
<!--em标签用来定义斜体-->
<p>em标签用来定义<em>斜体</em></p>
```
### HTML“计算机输出”标签

|元素|作用|
|:---|:--|
|code元素|定义计算机代码|
|kbd元素|定义键盘码|
|samp元素|定义计算机代码样本|
|var元素|定义变量|
|pre元素|定义预格式文本|

```
<!--<code>标签定义计算机代码-->
<code>This is a code</code>
<!--<kbd>标签定义键盘码-->
<p>Type the following in the Run dialog: <kbd>cmd</kbd><br />Then click the OK button.</p>
<p>Save the document by pressing <kbd>Ctrl</kbd> + <kbd>S</kbd></p>
<!--<samp>标签用来定义计算机代码样本-->
<p>Regular text. <samp>This is sample text.</samp> Regular text.</p>
<!--<var>标签用来定义变量-->
<p>A simple equation: <var>x</var> = <var>y</var> + 2 </p>
<!--<pre>标签定义预格式文本-->
<pre>
此例演示如何使用 pre 标签
对空行和    空格
进行控制
</pre>
```
### HTML“引文引用”标签

|元素|作用|
|:---|:--|
|abbr元素|定义缩写|
|address元素|定义地址|
|bdo元素|定义文字方向|
|blockquote元素|定义长的引用|
|q元素|定义短的引用语|
|cite元素|定义引用、引证|
|dfn元素|定义一个定义项目|

```
<!--<abbr>标签用于展示缩写-->
<p>You can use <abbr title="Cascading Style Sheets">CSS</abbr> to style your <abbr title="HyperText Markup Language">HTML</abbr>.</p>
<p>Using <abbr>HTML</abbr> is fun and easy!</p>
<!--<address>标签用于定义地址、URL、电子邮箱、电话号码、社交媒体账号、地理坐标等-->
<address>
    You can contact author at <a href="http://www.somedomain.com/contact">
    www.somedomain.com</a>.<br>
    If you see any bugs, please <a href="mailto:webmaster@somedomain.com">
    contact webmaster</a>.<br>
    You may also want to visit us:<br>
    Mozilla Foundation<br>
    331 E Evelyn Ave<br>
    Mountain View, CA 94041<br>
    USA
</address>

<p>Contact the author of this page:</p>
<address>
    <a href="mailto:jim@rock.com">jim@rock.com</a><br>
    <a href="tel:+13115552368">(311) 555-2368</a>
</address>

<!--<bdo>标签用于覆盖当前文本的朝向，它使得字符按给定的方向排列-->
<p>This text will go left to right.</p>
<p><bdo dir="rtl">This text will go right to left.</bdo></p>

<!--<blockquote>标签同样是对文本进行引用，不同的是该标签引用的是长文本，<blockquote>标签内的内容会自动缩进-->
<blockquote cite="http://www.worldwildlife.org/who/index.html">
    For 50 years, WWF has been protecting the future of nature. The world's leading conservation organization, WWF works in 100 countries and is supported by 1.2 million members in the United States and close to 5 million globally.
</blockquote>

<!--如果标记是不需要段落分隔的短引用，请使用<q>标签-->
<p>WWF's goal is to:
<q>Build a future where people live in harmony with nature.</q>
    We hope they succeed.
</p>

<!--<cite></cite>标签用于定义作品（比如书籍、歌曲、电影、电视节目、绘画、雕塑等）的标题，<city>标签内的文本将会以斜体显示。注意：人名不属于作品的标题-->
<p><cite>The Scream</cite> by Edward Munch. Painted in 1893.</p>
```
