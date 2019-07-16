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

