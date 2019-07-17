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
<dfn>定义项目</dfn>
```

### HTML 链接

HTML使用标签 <a>来设置超文本链接，是可以从一个页面指向另一个目的端的链接。超链接可以是一个字，一个词，或者一组词，也可以是一幅图像，您可以点击这些内容来跳转到新的文档或者当前文档中的某个部分。
  
当您把鼠标指针移动到网页中的某个链接上时，箭头会变为一只小手。在标签<a> 中使用了href属性来描述链接的地址。
  
默认情况下，链接将以以下形式出现在浏览器中：

* 一个未访问过的链接显示为蓝色字体并带有下划线;
* 访问过的链接显示为紫色并带上下划线;
* 点击链接时，链接显示为红色并带上下划线。

如果为这些超链接设置了CSS样式，展示样式会根据CSS的设定来显示。

**HTML 空链接**：HTML 空链接：指指向链接后，鼠标变成手形，但单击后仍停留在当前页面。

**HTML 链接 - target 属性**：
  使用 Target 属性，你可以定义被链接的文档在何处显示（在新的窗口打开，还是在原有的窗口中打开）。
如果为这些超链接设置了CSS样式，展示样式会根据CSS的设定来显示。

默认的被链接文档会在原有的窗口中打开的。如果将 target 属性设置为 "_blank" 则文档就会在新窗口打开。

**HTML 链接 - id 属性**：
  id 属性可用于在一个 HTML 文档中创建书签标记。书签是不以任何特殊的方式显示，在HTML文档中是不显示的，所以对于读者来说是隐藏的。
  
```
<!--空链接，#表示空链接-->
<a href="#">链接文字</a>
<br />
<a href="//www.baidu.com">Visit 百度</a>
<br />
<!--使用 Target 属性，你可以定义被链接的文档在何处显示（在新的窗口打开，还是在原有的窗口中打开）
默认的被链接文档会在原有的窗口中打开的。如果将 target 属性设置为 "_blank" 则文档就会在新窗口打开。-->
<a href="//www.w3cschool.cn" target="_blank">访问W3CSchool教程</a>
<br/>
<!--id 属性可用于在一个 HTML 文档中创建书签标记。书签是不以任何特殊的方式显示，在HTML文档中是不显示的，所以对于读者来说是隐藏的。-->
<a id="tips">Useful Tips Section</a>
<br/>
<a href="#tips">Visit the Useful Tips Section</a>
<br/>
<a href="//www.w3cschool.cn/html_links.html#tips"> Visit the Useful Tips Section</a>

<p>创建图片链接:
    <a href="http://www.w3cschool.cn/html/html-tutorial.html">
        <img src="/statics/images/course/smiley.gif" alt="HTML 教程" width="32" height="32">
    </a>
</p>
<p>无边框的图片链接:
    <a href="http://www.w3cschool.cn/html/html-tutorial.html">
        <img border="0" src="/statics/images/course/smiley.gif" alt="HTML 教程" width="132" height="132"></a></p>

<p>
    <a href="#C4">查看章节 4</a>
</p>

<h2>章节 1</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 2</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 3</h2>
<p>这边显示该章节的内容……</p>

<h2><a id="C4">章节 4</a></h2>
<p>这边显示该章节的内容……</p>

<h2>章节 5</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 6</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 7</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 8</h2>
<p>这边显示该章节的内容……</p>

<h2>章节 9</h2>
<p>这边显示该章节的内容……</p>

<p>
    这是一个电子邮箱链接
    <a href="mailto:1693288015@qq.com?Subject=Hello%20again" target="_top">发送邮件</a>
</p>
<b>注意:</b> 单词之间空格使用 %20 代替，以确保浏览器可以正常显示文本。
```
### HTML 头部 <head>

HTML 头部元素包含关于文档的概要信息，也被称为元信息（meta-information）。

|元素|作用|
|:---|:--|
|head元素|<head> 元素包含了所有的头部标签元素。在 <head>元素中你可以插入脚本（scripts）, 样式文件（CSS），及各种 meta 信息。其中包括文档的标题、在 Web 中的位置以及和其他文档的关系等|
|title元素|title标签定义了浏览器工具栏的标题。当网页添加到收藏夹时，显示在收藏夹中的标题。显示在搜索引擎结果页面的标题。|
|base元素|定义了页面链接标签的默认链接地址。该标签作为HTML文档中所有的链接标签的默认链接|
|link元素|定义了文档与外部资源之间的关系。通常用于链接到样式表。|
|style元素|定义了HTML文档的样式文件。在<style> 元素中你需要指定样式文件来渲染HTML文档。|
|meta元素|定义了HTML文档中的元数据。元数据也不显示在页面上，但会被浏览器解析。|
|script元素|定义了客户端的脚本文件。|
  
```
<head lang="en">
    <!--HTML<head>头部包含关于文档的概要信息，也被称为元信息(meta-information)。<head>元素包含了所有
的头部标签元素。在<head>元素中你可以插入脚本(script)，样式文件(CSS)以及各种meta信息。可以在头部
区域添加的元素标签为:<title>,<style>,<meta>,<link>,<script>,<noscript>和<base>。-->
    <!--<meta>标签：
    提供了元数据，也就是向下面例子中那样定义网页编码格式为utf-8。
    元数据也不显示在页面上，但会被浏览器解析。
    meta元素通常用于指定网页的描述，关键词，文件的最后修改时间，作者，和其他元数据。
    元数据可以使用于浏览器（如何显示内容或重新加载页面），搜索引擎（关键词），或其他Web服务。

    <meta> 标签- 使用实例:
    (1)为搜索引擎定义关键词:<meta name="keywords" content="HTML, CSS, XML, XHTML, JavaScript">
    (2)为网页定义描述内容:<meta name="description" content="Free Web tutorials on HTML and CSS">
    (3)定义网页作者:<meta name="author" content="Hege Refsnes">
    (4)每30秒钟刷新当前页面:<meta http-equiv="refresh" content="30">
    -->
    <meta charset="UTF-8">
    <!--<title>标签：
    定义了不同文档的标题。其在HTML/XHTML文档中是必须的。
    (1)定义了浏览器工具栏的标题
    (2)当网页添加到收藏夹时，显示在收藏夹中的标题
    (3)显示在搜索引擎结果页面的标题
    -->
    <title>MyFirstHTMLTitle™</title>
    <!--<base>标签：
    <base>标签描述了基本的链接地址/链接目标，该标签作为HTML文档中所有的链接标签的默认链接。
    在 HTML 中，<base> 标签没有结束标签；在 XHTML 中，<base> 标签必须被正确地关闭。
    -->
    <!--<base href="https://github.com/baohenglin" target="_blank">-->
    <!--<link标签：
        <link> 标签定义了文档与外部资源之间的关系。
        <link> 标签通常用于链接到样式表:
   -->
    <link rel="stylesheet" type="text/css" href="mystyle.css">
    <!--<style>标签：
    <style> 标签定义了HTML文档的样式文件引用地址.在<style> 元素中你需要指定样式文件来渲染HTML文档:
    -->
    <!--CSS内部样式表-->
    <style type="text/css">
        body {background-color: white}
        h1 {color: red}
        /*设置<p>标签定义的段落正文的字体颜色*/
        /*p {color: purple}*/
    </style>
    <!--<script>标签：
        <script>标签用于链接外部JavaScript文件的外部资源。<script> 元素既可包含脚本语句，也可以通过 "src" 属性
        指向外部脚本文件。
        假如 <script> 元素内部的代码没有位于某个函数中，那么这些代码会在页面被加载时被立即执行。
        <frameset> 标签之后的脚本会被忽略。

        有多种执行外部脚本的方法：
            (1)如果 async="async"：脚本相对于页面的其余部分异步地执行（当页面继续进行解析时，脚本将被执行）
            (2)如果不使用 async 且 defer="defer"：脚本将在页面完成解析时执行
            (3)如果既不使用 async 也不使用 defer：在浏览器继续解析页面之前，立即读取并执行脚本
    -->
    <script type="text/javascript" src="jquery.js"></script>
    <!--<script type = "text/javascript">-->
        <!--alert("php中文网\n网址为：www.php.cn");-->
    <!--</script>-->

    <!--noscript 元素:
    用来定义在脚本未被执行时的替代内容（文本）。
    此标签可被用于可识别 <noscript> 标签但无法支持其中的脚本的浏览器。
    -->
</head>
```

