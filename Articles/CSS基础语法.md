## CSS简介

CSS 指层叠样式表（Cascading Style Sheets）。 在我们的 CSS 教程中，您将学习如何使用 CSS 同时控制整个站点的样式和布局。 通过与 XHTML 结合，CSS 可以帮助我们实行表现与结构分离的开发模式。 通过使用 CSS 来提升工作效率！CSS 是一种用来表现HTML或XML的标记语言，属于浏览器解释型语言，可以直接由浏览器执行，不需要编译。样式定义如何显示 HTML 元素。样式通常存储在样式表中。

## 使用CSS的优势

* (1)内容与表现分离，有了CSS，网页的内容(XHMTL)与表现就可以分开了。使用CSS可以减少网页的代码量，增加网页的浏览速度。CSS样式表极大的提高了编码效率。
* (2)多页面应用同一个样式。通常保存在外部的独立的.css文件（该文件不属于任何页面文件）可以在多个页面中使用同一个CSS样式表。通过在任何的页面文件中引用.css文件，你可以设置具有一致风格的多个页面。

## 如何使用CSS？

有三种方法可以在站点网页上使用样式表：

*（1）外联式Linking（也叫外部样式）：将网页链接到外部样式表。
*（2）嵌入式Embedding（也叫内页样式）：在网页上创建嵌入的样式表。
*（3）内联式Inline（也叫行内样式）：应用内嵌样式到各个网页元素。

其中，优先级：内联式 > 嵌入式 > 外联式。

## 样式层叠次序

一般而言，所有的样式会根据下面的规则层叠于一个新的虚拟样式表中，优先级从高到低依次为：

* (1)内联样式（在 HTML 元素内部）
* (2)内部样式表（位于 <head> 标签内部）
* (3)外部样式表
* (4)浏览器缺省设置

因此，内联样式（在 HTML 元素内部）拥有最高的优先权，这意味着它将优先于以下的样式声明：<head> 标签中的样式声明，外部样式表中的样式声明，或者浏览器中的样式声明（缺省值）。
  
## CSS语法

CSS 规则由两个主要的部分构成：选择器(Selector)，以及一条或多条声明:

```       
 h1 {color: purple; font-size: 28px}
```

其中，选择器Selector通常是您需要改变样式的 HTML 元素。h1是Selector。每条声明由一个属性和一个值组成。

属性（property）是您希望设置的样式属性（style attribute）。每个属性有一个值。属性和值被冒号分开。color和font-size是属性，它们所对应的值分别为冒号后面的purple和28px。


## id 选择器:
        
id 选择器可以为标有特定 id 的 HTML 元素指定特定的样式。HTML元素以id属性来设置id选择器,CSS 中 id选择器以 "#" 来定义。ID属性不要以数字开头，数字开头的ID在 Mozilla/Firefox 浏览器中不起作用。ID属性只能在每个 HTML 文档中出现一次。

```
<h1 id="titleOne">CSS语法2</h1>
#titleOne {color: green}
```

## class选择器：
        
 class 选择器用于描述一组元素的样式，class 选择器有别于id选择器，class可以在多个元素中使用。class 选择器在HTML中以class属性表示, 在 CSS 中，类选择器以一个点"."号显示。类名的第一个字符不能使用数字！它无法在 Mozilla 或 Firefox 中起作用。
 
 ```
 /*所有拥有 center 类的 HTML 元素均为居中。*/
.center {text-align:center;}
```
 
 ## 如何插入样式表？
 
 三种方式：内联样式、内部样式表、外部样式表。

(1)内联样式：

要使用内联样式，你需要在相关的标签内使用样式（style）属性。Style 属性可以包含任何 CSS 属性。例如：

```
<p style="color:sienna;margin-left:20px">这是一个段落。</p>
```
        
由于要将表现和内容混杂在一起，内联样式会损失掉样式表的许多优势。请慎用这种方法，只有当样式仅需要在一个元素上应用一次时，才可以使用内联样式。

(2)内部样式表：

当单个文档需要特殊的样式时，就应该使用内部样式表。你可以使用 <style> 标签在文档头部定义内部样式表。

(3)外部样式表：

当样式需要应用于很多页面时，外部样式表将是理想的选择。在使用外部样式表的情况下，你可以通过改变一个文件来改变整个站点的外观。每个页面使用 标签链接到样式表。 标签在（文档的）头部。如：

```
<head> <link rel="stylesheet" type="text/css" href="mystyle.css"> </head>
```

浏览器会从文件 mystyle.css 中读到样式声明，并根据它来格式文档。外部样式表可以在任何文本编辑器中进行编辑。
 
文件不能包含任何的 html 标签。样式表应该以 .css 扩展名进行保存。例如：

```
hr {color:sienna;}
p {margin-left:20px;}
body {background-image:url(/images/back40.gif);}
```
            
【注意】不要在属性值与单位之间留有空格。假如你使用 "margin-left: 20 px" 而不是 "margin-left: 20px" ，它仅在 IE 6 中有效，但是在 Mozilla/Firefox 或 Netscape 中却无法正常工作。
 
 
## 多重样式将层叠为一个

样式表允许以多种方式规定样式信息。样式可以规定在单个的 HTML 元素中，在 HTML 页的头元素中，或在一个外部的 CSS 文件中。甚至可以在同一个 HTML 文档内部引用多个外部样式表。

(1)层叠次序：
    
一般而言，所有的样式会根据下面的规则层叠于一个新的虚拟样式表中，优先级从高到低依次为：
        
  * (1)内联样式（在 HTML 元素内部）
  * (2)内部样式表（位于 <head> 标签内部）
  * (3)外部样式表
  * (4)浏览器缺省设置
       
  因此，内联样式（在 HTML 元素内部）拥有最高的优先权，这意味着它将优先于以下的样式声明：<head> 标签中的样式声明，外部样式表中的样式声明，或者浏览器中的样式声明（缺省值）。如果你使用了外部文件的样式在内部样式中也定义了该样式，则内部样式表会取代外部文件的样式。

 (2)多重样式优先级深入理解：
    
  多重样式优先级顺序：下列是一份优先级逐级增加的选择器列表，其中数字 7 拥有最高的优先权：
        
  * 1.通用选择器（*）
  * 2.元素(类型)选择器
  * 3.类选择器
  * 4.属性选择器
  * 5.伪类
  * 6.ID 选择器
  * 7.内联样式

    【注意】!important 规则例外。当 !important 规则被应用在一个样式声明中时，该样式声明会覆盖CSS中任何其他的声明，无论它处在声明列表中的哪里。尽管如此，!important规则还是与优先级毫无关系。使用 !important 不是一个好习惯，因为它改变了你样式表本来的级联规则，从而使其难以调试。
    
【总结】要优化考虑使用样式规则的优先级来解决问题而不是 !important；只在需要覆盖全站或外部 css（例如引用的 ExtJs 或者 YUI ）的特定页面中使用 !important；永远不要在全站范围的 css 上使用 !important；永远不要在你的插件中使用 !important。

(3)权重计算：内联样式表的权值最高 1000；ID 选择器的权值为 100；Class 类选择器的权值为 10；HTML 标签选择器的权值为 1。

(4)CSS 优先级法则：
    
 * A 选择器都有一个权值，权值越大越优先；
 * B 当权值相等时，后出现的样式表设置要优于先出现的样式表设置；
 * C 创作者的规则高于浏览者：即网页编写者设置的CSS 样式的优先权高于浏览器所设置的样式；
 * D 继承的CSS 样式不如后来指定的CSS 样式；
 * E 在同一组属性设置中标有"!important"规则的优先级最大；
       
## CSS Backgrounds(背景):

CSS 背景属性用于定义HTML元素的背景。CSS 属性定义背景效果：

* background               简写属性，作用是将背景属性设置在一个声明中。
* background-color         设置元素的背景颜色。
* background-image         把图像设置为背景。
* background-repeat        设置背景图像是否及如何重复。
* background-attachment    背景图像是否固定或者随着页面的其余部分滚动。
* background-position      设置背景图像的起始位置。

(1)背景颜色background-color:

background-color 属性定义了元素的背景颜色。页面的背景颜色使用在body的选择器中。background-color 不能继承，其默认值是 transparent。如果一个元素没有指定背景色，那么背景就是透明的，这样其父元素的背景才可见。

(2)背景图像background-image:

background-image 属性描述了元素的背景图像.默认情况下，背景图像进行平铺重复显示，以覆盖整个元素实体.

```
body {background-image:url('paper.gif');}
```

(3)背景图像 - 水平或垂直平铺background-repeat

如果需要在HTML页面上对背景图像进行平铺，可以使用 background-repeat 属性。默认情况下background-image 属性会在页面的水平或者垂直方向平铺。为了让页面排版更加合理，不影响文本的阅读，我们可以改变图像的位置。可以利用 background-position 属性改变图像在背景中的位置。

为 background-position 属性提供值的方法有很多。首先，可以使用一些关键字：top、bottom、left、right和 center；其次，可以使用长度值，如 100px 或 5cm；最后也可以使用百分数值。不同类型的值对于背景图像的放置稍有差异。

如果设置值为 50px 100px，图像的左上角将在元素内边距区左上角向右 50 像素、向下 100 像素的位置上。
       
```
{
  background-color: black;
  background-image: url("imagesFile/RedPacketListImg.jpg");
  background-repeat: no-repeat;
  background-position: 20px -200px;

  background: black url("imagesFile/RedPacketListImg.jpg") no-repeat top right;
}
```
 ## CSS 文本属性
 
 (1)CSS Text文本格式：
 
  通过CSS的Text属性，你可以改变页面中文本的颜色、字符间距、对齐文本、装饰文本、对文本进行缩进等。

 (2)Text Color：颜色属性被用来设置文字的颜色。对于W3C标准的CSS：如果你定义了颜色属性，你还必须定义背景色属性。

 (3)文本的对齐方式：
 
 文本排列属性是用来设置文本的水平对齐方式。文本可居中或对齐到左或右,两端对齐.当text-align设置为"justify"，每一行被展开为宽度相等，左，右外边距是对齐（如杂志和报纸）。如果想把一个行内元素的第一行“缩进”，可以用左内边距或外边距创造这种效果。

(4)文本修饰
      
 text-decoration 属性用来设置或删除文本的装饰。从设计的角度看 text-decoration属性主要是用来删除链接的下划线。
        
```
 a {text-decoration:none;}//去掉链接的下划线。
```

 (5)文本转换
        
 文本转换属性是用来指定在一个文本中的大写和小写字母。可用于所有字句变成大写或小写字母，或每个单词的首字母大写。

 (6)文本缩进
        
 文本缩进属性是用来指定文本的第一行的缩进。CSS 提供了 text-indent 属性，该属性可以方便地实现文本缩进。通过使用text-indent 属性，所有元素的第一行都可以缩进一个给定的长度。

 (7)文本间隔
        
 word-spacing 属性可以改变字（单词）之间的标准间隔。其默认值 normal 与设置值为 0 是一样的。

 (8)CSS所有文本属性：
 
  * color           设置文本颜色
  * direction       设置文本方向
  * letter-spacing  设置字符间距
  * line-height     设置行高
  * text-align      对齐元素中的文本
  * text-decoration 向文本添加修饰
  * text-indent     缩进元素中文本的首行
  * text-shadow     设置文本阴影
  * text-transform  控制元素中的字母
  * unicode-bidi    设置或返回文本是否被重写
  * vertical-align  设置元素的垂直对齐
  * white-space     设置元素中空白的处理方式
  * word-spacing    设置字间距
  
  ```
  h1 {
    text-align: center;
    text-decoration: underline
}
p {
    text-indent: 10px
}
p.date {
    text-align: right
}
/*
text-indent 首行缩进
line-height 行间距
*/
p.main {
    text-align: justify;
    text-indent: 2em;
    line-height: 2em;
    /*word-spacing: 10px;*/
    /*设置字体加粗(bold)、变细(lighter)、正常(normal)，也可以设置数字）*/
    font-weight: normal;
}
p.main {
    text-transform: uppercase;
    font-family: serif, Times, "Times New Roman";
    font-style: italic;
}
```
 
## CSS Fonts(字体)：

CSS字体属性定义字体，加粗，大小，文字样式。在计算机屏幕上，sans-serif字体被认为是比serif字体容易阅读。

1.CSS字型：

在CSS中，有两种类型的字体系列名称：

* (1)通用字体系列 - 拥有相似外观的字体系统组合（如 "Serif" 或 "Monospace"）。
* (2)特定字体系列 - 一个特定的字体系列（如 "Times" 或 "Courier"）。

除了各种特定的字体系列外，CSS 定义了 5 种通用字体系列：

 * Serif 字体
 * Sans-serif 字体
 * Monospace 字体
 * Cursive 字体
 * Fantasy 字体

2.字体系列：

  font-family 属性设置文本的字体系列。font-family 属性应该设置几个字体名称作为一种"后备"机制，如果浏览器不支持第一种字体，它将尝试下一种字体。以确保浏览器/操作系统之间的最大兼容性。
    
  如果字体系列的名称超过一个字，它必须用引号，如Font Family："宋体"。多个字体系列是用一个逗号分隔指明。
  
  ```
  p{font-family:"Times New Roman", Times, serif;}
  ```

3.字体样式：

  主要是用于指定斜体文字的字体样式属性。这个属性有三个值：
  
  * normal(正常)          - 正常显示文本
  * italic(斜体)          - 以斜体字显示的文字
  * oblique(倾斜的文字)    - 文字向一边倾斜（和斜体非常类似，但不太支持）

  italic 和 oblique 的区别:
  
斜体（italic）是一种简单的字体风格，对每个字母的结构有一些小改动，来反映变化的外观。倾斜（oblique）文本是正常竖直文本的一个倾斜版本。通常情况下，italic 和 oblique 文本在 web 浏览器中看上去完全一样。
  

4.字体大小:

font-size 属性设置文本的大小。能否管理文字的大小，在网页设计中是非常重要的。但是，你不能通过调整字体大小使段落看上去像标题，或者使标题看上去像段落。请务必使用正确的HTML标签，比如h1 - h6元素用来表示标题和p元素用来表示段落。字体大小的值可以是绝对或相对的大小。

* 绝对大小：设置一个指定大小的文本，不允许用户在所有浏览器中改变文本大小，确定了输出的物理尺寸时绝对大小很有用

* 相对大小：相对于周围的元素来设置大小，允许用户在浏览器中改变文字大小。

如果你不指定一个字体的大小，默认大小和普通文本段落一样，是16像素（16px=1em）。


5.设置字体大小像素：

设置文字的大小与像素，让您完全控制文字大小。如：

```
    h1 {font-size:40px;}
    h2 {font-size:30px;}
    p {font-size:14px;}
```

上面的例子可以在 Internet Explorer 9, Firefox, Chrome, Opera, 和 Safari 调整文本大小。以上实例不能在IE9以前的版本运行。虽然可以通过浏览器的缩放工具调整文本大小，但是，这种调整是整个页面，而不仅仅是文本。

6.用em来设置字体大小：

为了避免Internet Explorer 中无法调整文本的问题，许多开发者使用 em 单位代替像素。1em和当前字体大小相等。在浏览器中默认的文字大小是16px。因此，1em的默认大小是16px。可以通过下面这个公式将像素转换为em：px/16=em。

【注意】16 等于父元素的默认字体大小，假设父元素的 font-size 为 30px，那么公式需改为：pixels/30=em。

7.使用百分比和EM组合:

在所有浏览器的解决方案中，设置 <body>元素的默认字体大小的是百分比。
  
```
body {font-size:100%;}
h1 {font-size:2.5em;}
h2 {font-size:1.875em;}
p {font-size:0.875em;}
```

8.所有CSS字体属性：

```
font            在一个声明中设置所有的字体属性
font-family     指定文本的字体系列
font-size       指定文本的字体大小
font-style      指定文本的字体样式
font-variant    以小型大写字体或者正常字体显示文本
font-weight     指定字体的粗细。
```

## DTD概念：

  DTD= Document Type Definition，文档类型声明。DTD作用是：它定义以哪种标准解析document，确保document在不同的浏览器中以相同的方式被解析。

  HTML5 不基于 SGML，所以不需要引用 DTD。

  三种DTD类型的作用说明：
  
  * Strict : 干净的标记，免于表现层的混乱，与层叠样式表（CSS）配合使用。
  * Transitional :包含 W3C 所期望移入样式表的呈现属性和元素。使用不支持层叠样式表（CSS）的浏览器时使用。
  * Frameset : 应当被用于带有框架的文档。除 frameset 元素取代了 body 元素之外，Frameset等同于 Transitional。
  
## CSS链接：

不同的链接可以有不同的样式。

1.链接样式

链接的样式，可以用任何CSS属性（如颜色，字体，背景等）。这四个链接状态是：

   * a:link - 正常，未访问过的链接
   * a:visited - 用户已访问过的链接
   * a:hover - 当用户鼠标放在链接上时
   * a:active - 链接被点击的那一刻

当设置为若干链路状态的样式，也有一些顺序规则：a:hover 必须跟在 a:link 和 a:visited后面。a:active 必须跟在 a:hover后面。

2.常见的链接样式

根据上述链接的颜色变化的例子，看它是在什么状态。让我们通过一些其他常见的方式转到链接样式。

文本修饰：text-decoration 属性主要用于删除链接中的下划线。

背景颜色：背景颜色属性指定链接背景色。

```
a:link {background-color:#B2FF99; text-decoration:none;}
a:visited {background-color:#FFFF85; text-decoration:none;}
a:hover {background-color:#FF704D; text-decoration:underline;}
a:active {background-color:#FF704D; text-decoration:underline;}

a.one:link {color:#ff0000;}
a.one:visited {color:#0000ff;}
a.one:hover {color:#ffcc00;}

a.two:link {color:#ff0000;}
a.two:visited {color:#0000ff;}
a.two:hover {font-size:150%;}

a.three:link {color:#ff0000;}
a.three:visited {color:#0000ff;}
a.three:hover {background:#66ff66;}

a.four:link {color:#ff0000;}
a.four:visited {color:#0000ff;}
a.four:hover {font-family:monospace;}

a.five:link {color:#ff0000;text-decoration:none;}
a.five:visited {color:#0000ff;text-decoration:none;}
a.five:hover {text-decoration:underline;}
```
## CSS列表样式(ul和ol):

在HTML中，有两种类型的HTML列表：

  * (1)无序列表 - 列表项标记用特殊图形（如小黑点、小方框等）
  * (2)有序列表 - 列表项的标记有数字或字母
  
  使用CSS，可以列出进一步的样式，并可用图像作列表项标记。

  1.不同的列表项标记:
  
  list-style-type属性指定列表项标记的类型。下列是对list-style-type属性的常见属性值的描述：
  
  * none：不使用项目符号
  * disc：实心圆
  * circle：空心圆
  * square：实心方块
  * demical：阿拉伯数字
  * lower-alpha：小写英文字母
  * upper-alpha：大写英文字母
  * lower-roman：小写罗马数字
  * upper-roman：大写罗马数字

  2.作为列表项标记的图像：
  
  要指定列表项标记的图像，使用列表样式图像属性list-style-image。如果你想在所有的浏览器放置同样的形象标志，就应使用浏览器兼容性解决方案。

  3.浏览器兼容性解决方案：list-style-type,padding,margin,background-image,background-repeat,background-position,padding-left

  4.列表-缩写属性
  
  在单个属性中可以指定所有的列表属性。这就是所谓的缩写属性。如果使用缩写属性值的顺序是：
  
  * (1)list-style-type
  * (2)list-style-position (有关说明，请参见下面的CSS属性表)
  * (3)list-style-image
      
  如果上述值丢失一个，其余仍在指定的顺序，就没关系。

  5.所有CSS列表属性：

  * list-style              简写属性。用于把所有用于列表的属性设置于一个声明中
  * list-style-image        将图象设置为列表项标志
  * list-style-position     设置列表中列表项标志的位置
  * list-style-type         设置列表项标志的类型 

  
```
<p>无序列表实例</p>
<ul class="a">
    <li>Coffee</li>
    <li>Tea</li>
    <li>CocaCola</li>
</ul>

<ul class="b">
    <li>Coffee</li>
    <li>Tea</li>
    <li>CocaCola</li>
</ul>

<p>有序列表实例</p>
<ol class="c">
    <li>Coffee</li>
    <li>Tea</li>
    <li>CocaCola</li>
</ol>

<ol class="d">
    <li>Coffee</li>
    <li>Tea</li>
    <li>CocaCola</li>
</ol>

ul.a {list-style-type: circle}
ul.b {list-style-type: square}
ol.c {list-style-type: upper-roman}
ol.d {list-style-type: lower-alpha}
/*设置列表样式类型为没有列表项标记,设置填充和边距0px（浏览器兼容性）*/
ul {
    list-style-image: url("imagesFile/share_wechat.png");
    list-style-type:none;
    padding:0px;
    margin:0px;
}
```

ul中所有li:设置图像的URL，并设置它只显示一次（无重复）,您需要的定位图像位置（左0px和上下5px）用padding-left属性把文本置于列表中

```
ul li {
    background-image:url("imagesFile/share_wechat.png");
    background-repeat:no-repeat;
    background-position:0px 5px;
    padding-left:14px;
}
```

## Table表格

1.表格边框:指定CSS表格边框，使用border属性。

2.折叠边框:border-collapse 属性设置表格的边框是否被折叠成一个单一的边框或隔开。

3.表格宽度:定义表格的宽度

4.表格高度:定义表格的高度

5.表格文字对齐:表格中的文本对齐和垂直对齐属性。

  * text-align属性设置水平对齐方式，像左，右，或中心。
  * vertical-align垂直对齐属性设置垂直对齐
  
6.表格填充：如果在表的内容中控制空格之间的边框，应使用td和th元素的填充属性。
```
  td { padding:15px; }
```

7.表格颜色：
```
table, th, td{border: 1px solid green;}
```

```
<table>
    <caption>Table 1.1</caption>
    <tr>
        <th>Firstname</th>
        <th>Lastname</th>
    </tr>
    <tr>
        <td>Peter</td>
        <td>Jack</td>
    </tr>
    <tr>
        <td>Rose</td>
        <td>Lois</td>
    </tr>
</table>

//CSS文件中
table, th, td {
    /*例子中的表格有双边框。这是因为表和th/ td元素有独立的边界。*/
    border: 1px solid green;
}
table {
    /*border-collapse 属性设置表格的边框是否被折叠成一个单一的边框或隔开*/
    border-collapse: collapse;
    width: 100%;
}
th {
    /*height: 50px;*/
    background-color: darkgreen;
    color: white;
}
td {
    /*水平对齐方式*/
    text-align: center;
    /*垂直对齐方式*/
    vertical-align: middle;
    /*height: 100px;*/
    /*设置表格填充*/
    padding: 15px;
}
```

国家列表示例：

```
<table id="customers">
    <caption>Table 1.2</caption>
    <tr>
        <th>Company</th>
        <th>Contact</th>
        <th>Country</th>
    </tr>
    <tr>
        <td>Alfreds Futterkiste</td>
        <td>Maria Anders</td>
        <td>Germany</td>
    </tr>
    <tr class="alt">
        <td>Berglunds snabbköp</td>
        <td>Christina Berglund</td>
        <td>Sweden</td>
    </tr>
    <tr>
        <td>Centro comercial Moctezuma</td>
        <td>Francisco Chang</td>
        <td>Mexico</td>
    </tr>
    <tr class="alt">
        <td>Ernst Handel</td>
        <td>Roland Mendel</td>
        <td>Austria</td>
    </tr>
    <tr>
        <td>Island Trading</td>
        <td>Helen Bennett</td>
        <td>UK</td>
    </tr>
    <tr class="alt">
        <td>Königlich Essen</td>
        <td>Philip Cramer</td>
        <td>Germany</td>
    </tr>
    <tr>
        <td>Laughing Bacchus Winecellars</td>
        <td>Yoshi Tannamuri</td>
        <td>Canada</td>
    </tr>
    <tr class="alt">
        <td>Magazzini Alimentari Riuniti</td>
        <td>Giovanni Rovelli</td>
        <td>Italy</td>
    </tr>
    <tr>
        <td>North/South</td>
        <td>Simon Crowther</td>
        <td>UK</td>
    </tr>
    <tr class="alt">
        <td>Paris spécialités</td>
        <td>Marie Bertrand</td>
        <td>France</td>
    </tr>
</table>
//CSS文件
/*国家列表*/
#customers
{
    font-family:"Trebuchet MS", Arial, Helvetica, sans-serif;
    width:100%;
    border-collapse:collapse;
}
#customers td, #customers th
{
    font-size:1em;
    border:1px solid #98bf21;
    padding:3px 7px 2px 7px;
}
#customers th
{
    font-size:1.1em;
    text-align:left;
    padding-top:5px;
    padding-bottom:4px;
    background-color:#A7C942;
    color:#ffffff;
}
#customers tr.alt td
{
    color:#000000;
    background-color:#EAF2D3;
}
caption
{
    /*设置表格标题位置*/
    caption-side: bottom;
}
```

## CSS盒子模型（Box Model）：

所有HTML元素可以看作盒子，在CSS中，"box model"这一术语是用来设计和布局时使用。CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：边距，边框，填充，和实际内容。盒模型允许我们在其它元素和周围元素边框之间的空间放置元素。

在页面设计中有4个常见属性：content（内容）、padding（内边距）、border（边框）和margin（外边距），我们把这4部分转化成日常生活的盒子来理解，所以称为盒子模型。

盒子模型结构：

* Margin（外边距） - 清除边框区域。Margin没有背景颜色，它是完全透明。
* Border（边框） - 边框周围的填充和内容。边框是受到盒子的背景颜色影响。
* Padding（内边距） - 清除内容周围的区域。会受到框中填充的背景颜色影响。
* Content（内容） - 盒子的内容，显示文本和图像。

为了在所有浏览器中的元素的宽度和高度设置正确的话，你需要知道的盒模型是如何工作的。在盒模型中，外边距可以是负值，而且在很多情况下都要使用负值的外边距。

一个盒子实际所占有的宽度（或高度）是由“内容+内边距+边框+外边距”组成的。

在网页设计中，content常指文字、图片等元素，但是也可以是小盒子（DIV嵌套），padding只有宽度属性，可以理解为真实盒子中抗震辅料的厚度，而border有大小和颜色之分，又可以理解为真实盒子的厚度以及这个盒子的颜色或材料，margin就是该盒子与其他东西要保留多大距离。

在CSS中可以通过设置width和height的值来控制内容所占矩形的大小，并且对于任何一个盒子，都可以分别设定4条边各自的border、padding和margin。因此只要利用好这些属性，就能够实现各种各样的排版效果。

[CSS盒子模型科普](https://www.w3cschool.cn/css_series/css_series-6tza24q1.html)

## CSS Border（边框）：
    
CSS 边框 (border) 可以是围绕元素内容和内边距的一条或多条线，对于这些线条，您可以自定义它们的样式、宽度以及颜色。使用CSS边框属性，我们可以创建出比HTML中更加优秀的效果。

1.边框样式：

边框样式属性指定要显示什么样的边界。border-style属性用来定义边框的样式。border-style 值包括以下几种:

* (1)none: 默认无边框
* (2)dotted: 定义一个点线框
* (3)dashed: 定义一个虚线框
* (4)solid: 定义实线边界
* (5)double: 定义两个边界。 两个边界的宽度和border-width的值相同
* (6)groove: 定义3D沟槽边界。效果取决于边界的颜色值
* (7)ridge: 定义3D脊边界。效果取决于边界的颜色值
* (8)inset:定义一个3D的嵌入边框。效果取决于边界的颜色值
* (9)outset: 定义一个3D突出边框。 效果取决于边界的颜色值

2.边框宽度:

可以通过 border-width 属性为边框指定宽度。为边框指定宽度有两种方法：可以指定长度值，比如 2px 或 0.1em；或者使用 3 个关键字之一，它们分别是 thin 、medium（默认值） 和 thick。

【注意】CSS 没有定义 3 个关键字的具体宽度，所以一个用户代理可能把 thin(细) 、medium(默认) 和 thick(粗) 分别设置为等于 5px、3px 和 2px，而另一个用户代理则分别设置为 3px、2px 和 1px。

3.边框颜色：

border-color属性用于设置边框的颜色，它一次可以接受最多 4 个颜色值。可以设置的颜色。【注意】border-color单独使用是不起作用的，必须得先使用border-style来设置边框样式。

4.边框-单独设置各边：

在CSS中，可以指定不同的侧面不同的边框。border-style属性可以有1-4个值：

* (1)border-style:dotted solid double dashed;表示上边框是 dotted,右边框是 solid,底边框是 double,左边框是 dashed
* (2)border-style:dotted solid double;表示上边框是 dotted,左、右边框是 solid,底边框是 double
* (3)border-style:dotted solid;表示上、底边框是 dotted,左、右边框是 solid
* (4)border-style:dotted;表示四面边框是 dotted。

5.透明边框

CSS2 引入了边框颜色值 transparent，这个值用于创建有宽度的不可见边框。利用 transparent，使用边框就像是额外的内边距一样；此外还有一个好处，就是能在你需要的时候使其可见。这种透明边框相当于内边距，因为元素的背景会延伸到边框区域（如果有可见背景的话）。

6.CSS边框属性总结：

* border              简写属性，用于把针对四个边的属性设置在一个声明。
* border-style        用于设置元素所有边框的样式，或者单独地为各边设置边框样式。
* border-width        简写属性，用于为元素的所有边框设置宽度，或者单独地为各边边框设置宽度。
* border-color        简写属性，设置元素的所有边框中可见部分的颜色，或为 4 个边分别设置颜色。
* border-bottom       简写属性，用于把下边框的所有属性设置到一个声明中。
* border-bottom-color 设置元素的下边框的颜色。
* border-bottom-style 设置元素的下边框的样式。
* border-bottom-width 设置元素的下边框的宽度。
* border-left         简写属性，用于把左边框的所有属性设置到一个声明中。
* border-left-color   设置元素的左边框的颜色。
* border-left-style   设置元素的左边框的样式。
* border-left-width   设置元素的左边框的宽度。
* border-right        简写属性，用于把右边框的所有属性设置到一个声明中。
* border-right-color  设置元素的右边框的颜色。
* border-right-style  设置元素的右边框的样式
* border-right-width  设置元素的右边框的宽度
* border-top          简写属性，用于把上边框的所有属性设置到一个声明中。
* border-top-color    设置元素的上边框的颜色。
* border-top-style    设置元素的上边框的样式。
* border-top-width    设置元素的上边框的宽度。

```
<p class="none">No border.</p>
<p class="dotted">A dotted border.</p>
<p class="dashed">A dashed border.</p>
<p class="solid">A solid border.</p>
<p class="double">A double border.</p>
<p class="groove">A groove border.</p>
<p class="ridge">A ridge border.</p>
<p class="inset">An inset border.</p>
<p class="outset">An outset border.</p>
<p class="hidden">A hidden border.</p>

//CSS文件
p.none {border-style:none;}
p.dotted {border-style:dotted;}
p.dashed {border-style:dashed; border-color: green; border-width: 5px}
p.solid {border-style:solid; border-width: thick}
p.double {border-style:double;}
p.groove {border-style:groove;}
p.ridge {border-style:ridge;}
p.inset {border-style:inset;}
p.outset {border-style:outset;}
p.hidden {border-style:hidden;}
/*边框-简写属性*/
p.solid {border: 10px solid #ff0000}
/*指定不同的侧面不同的边框*/
p.solid
{
    border-top-style: dotted;
    border-right-style: solid;
    border-bottom-style: dotted;
    border-left-style: solid;
}
```

## CSS轮廓(outline)属性

轮廓（outline）是绘制于元素周围的一条线，位于边框边缘的外围，可起到突出元素的作用。轮廓（outline）属性指定了样式，颜色和外边框的宽度。轮廓（outline）属性的位置让它不像边框那样参与到文档流中，因此轮廓出现或消失时不会影响文档流，即不会导致文档的重新显示。

outline用于一个声明中设置所有轮廓属性。包括outline-color、outline-style、outline-width、inherit。

outline-style用于设置轮廓的样式。包括none、dotted、dashed、solid、double、groove、ridge、inset、outset、inherit。

outline-width用于设置轮廓的宽度。包括thin、medium、thick、length、inherit。

outline-color用于设置轮廓的颜色。

```
p {
    border: 1px solid #ff0000;
    outline: green dotted thick;
}
```

## CSS Margin(外边距)

CSS Margin(外边距)属性定义元素周围的空间。CSS Margin属性接受任何长度单位、百分数值甚至负值。margin没有背景颜色，是完全透明的。margin可以单独改变元素的上，下，左，右边距。也可以一次改变所有的属性。

Margin可能的值包括：

* (1)auto：设置浏览器边距。这样做的结果会依赖于浏览器
* (2)length：定义一个固定的margin（使用像素，pt，em等）
* (3)百分比：定义一个使用百分比的边距。

Margin可以使用负值，即重叠的内容。

## CSS Padding（填充）：

CSS Padding（填充）属性定义元素边框与元素内容之间的空间。当元素的 Padding（填充）（内边距）被清除时，所"释放"的区域将会受到元素背景颜色的填充。单独使用填充属性可以改变上下左右的填充。缩写填充属性也可以使用，一旦改变一切都改变。

Padding可能的值包括length和百分比。其中length定义一个固定的填充(像素, pt, em,等)。CSS padding属性的百分比数值是相对于其父元素的 width 计算的，如果改变了父元素的 width，则它们也会改变。

【注意】CSS padding 属性可以使用长度值或百分比值，但与 margin 属性不同，它不允许使用负值。

比如：

```
<div style="width: 200px;">
<p>This paragragh is contained within a DIV that has a width of 200 pixels.</p>
</div>
```

【注意】：上下内边距与左右内边距一致，即上下内边距的百分数会相对于父元素宽度设置，而不是相对于高度。



填充- 单边内边距属性：

在CSS中，它可以指定不同的侧面不同的填充。比如padding-top、padding-bottom、padding-right、padding-left。也可以采用简写属性，比如

```
padding:25px 50px 75px 100px;表示上填充为25px，右填充为50px，下填充为75px，左填充为100px。
padding:25px 50px 75px;表示上填充为25px，左右填充为50px，下填充为75px。
```

## CSS分组

CSS 分组可以将具有相同样式的选择器进行分组，减少代码量。

Grouping Selectors:

在样式表中有很多具有相同样式的元素。为了尽量减少代码，你可以使用分组选择器。每个选择器用逗号分隔。可以对任意多个选择器进行分组，CSS 对此没有任何限制。

```
h1
{
    color:green;
}
h2
{
    color:green;
}
p
{
    color:green;
}
/*以上代码可以通过分组选择器可简化为如下*/
h1, h2, p
{
    color: green;
}
```

## 嵌套选择器

嵌套选择器适用于选择器内部的选择器的样式。下面的例子，为所有p元素指定一个样式，为所有元素指定一个class="marked"的样式，并仅用于class="标记"，类内的p元素指定第三个样式:

```
p
{
    color:blue;
    text-align:center;
}
.marked
{
    background-color:red;
}
.marked p
{
    color:white;
}
p {border-style:none;}
```

## CSS 尺寸 (Dimension)

CSS 尺寸 (Dimension) 属性允许你控制元素的高度和宽度。同样，它允许你增加行间距。

所有CSS Dimension属性包括：

* height          设置元素的高度
* line-height     设置行高
* max-height      设置元素的最大高度
* max-width       设置元素的最大宽度
* min-height      设置元素的最小高度
* min-width       设置元素的最小宽度
* width           设置元素的宽度


## CSS Display(显示) 与 Visibility（可见性）

CSS display 属性和 visibility属性都可以用来隐藏某个元素，但是这两个属性有不同的定义。display属性设置一个元素应如何显示，visibility属性指定一个元素应可见还是隐藏。

1.隐藏元素 - display:none或visibility:hidden：

隐藏一个元素可以通过把display属性设置为"none"，或把visibility属性设置为"hidden"。但是请注意，这两种方法会产生不同的结果。

visibility:hidden可以隐藏某个元素，但隐藏的元素仍需占用与未隐藏之前一样的空间。也就是说，该元素虽然被隐藏了，但仍然会影响布局。display:none可以隐藏某个元素，且隐藏的元素不会占用任何空间。也就是说，该元素不但被隐藏了，而且该元素原本占用的空间也会从页面布局中消失。

```
<h1>This is a visible heading</h1>
<h1 class="hidden">This is a hidden heading</h1>
<p>注意,隐藏标题仍然占用空间.</p>

//CSS
h1.hidden {visibility:hidden;}
```

2.CSS Display - 块和内联元素

块元素是一个元素，占用了全部宽度，在前后都是换行符。块元素的例子：h1元素、p元素、div元素。内联元素只需要必要的宽度，不强制换行。内联元素的例子：span元素、a元素

可以更改内联元素为块元素，反之亦然，可以使页面看起来是以一种特定的方式组合，并仍然遵循web标准。

```
//例1 按照内联元素显示
<ul>
    <li><a href="/html/" target="_blank">HTML</a></li>
    <li><a href="/css/" target="_blank">CSS</a></li>
    <li><a href="/js/" target="_blank">JavaScript</a></li>
    <li><a href="/xml/" target="_blank">XML</a></li>
</ul>

//CSS
li{display:inline}
```

```
//例2 将p元素按照内联元素显示
<p>A display property with a value of "inline" results in</p>
<p>no distance between two elements.</p>
//CSS
p {display:inline;}
```

```
//例3 按照块元素显示
<h2>Nirvana</h2>
<span>Record: MTV Unplugged in New York</span>
<span>Year: 1993</span>
<h2>Radiohead</h2>
<span>Record: OK Computer</span>
<span>Year: 1997</span>

//CSS
/*把span元素作为块元素*/
span {display:block;}
```

## CSS Positioning(定位)

CSS position 属性，允许您将布局的一部分与另一部分重叠，还可以完成多年来通常需要使用多个表格才能完成的任务。CSS定位属性允许你为一个元素定位。它也可以将一个元素放在另一个元素后面，并指定一个元素的内容太大时，应该发生什么。

有四种不同的定位方法：

(1)Static定位，也就是HTML元素的默认值，即没有定位，元素出现在正常的流中。

(2）Fixed定位，元素的位置相对于浏览器窗口是固定位置。即使窗口是滚动的它也不会移动。【注意】Fixed 定位在 IE7 和 IE8 下需要描述 !DOCTYPE 才能支持。Fixed定位使元素的位置与文档流无关，因此不占据空间。Fixed定位的元素和其他元素重叠。

(3)Relative 定位。相对定位元素的定位是相对其正常位置。相对定位元素经常被用来作为绝对定位元素的容器块。

(4)Absolute 定位。绝对定位的元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于<html>


**重叠的元素**:

元素的定位与文档流无关，所以它们可以覆盖页面上的其它元素。z-index属性指定了一个元素的堆叠顺序（哪个元素应该放在前面，或后面）。一个元素可以有正数或负数的堆叠顺序。

【注意】具有更高堆叠顺序的元素总是在较低的堆叠顺序元素的前面。如果两个定位元素重叠，没有指定z - index，最后定位在HTML代码中的元素将被显示在最前面。

  
## CSS Float（浮动）：

CSS float 属性定义元素在哪个方向浮动，浮动元素会生成一个块级框，直到该块级框的外边缘碰到包含框或者其他的浮动框为止。CSS 的 Float（浮动），会使元素向左或向右移动，其周围的元素也会重新排列。Float（浮动），往往是用于图像，但它在布局时一样非常有用。


**元素怎样浮动？**

元素的水平方向浮动，意味着元素只能左右移动而不能上下移动。一个浮动元素会尽量向左或向右移动，直到它的外边缘碰到包含框或另一个浮动框的边框为止。浮动元素之后的元素将围绕它，浮动元素之前的元素将不会受到影响。


**彼此相邻的浮动元素：**

如果你把几个浮动的元素放到一起，如果有空间的话，它们将彼此相邻。

**清除浮动：**

元素浮动之后，周围的元素会重新排列，为了避免这种情况，使用 clear 属性。clear 属性指定元素两侧不能出现浮动元素。


```
<h3>图片库</h3>
<p>试着调整窗口,看看当图片没有足够的空间会发生什么。</p>
<br/>
<img class="thumbnail" src="imagesFile/RedPacketListImg.jpg" width="300" height="90">
<img class="thumbnail" src="imagesFile/RedPacketListImg.jpg" width="300" height="80">
<img class="thumbnail" src="imagesFile/RedPacketListImg.jpg" width="300" height="90">
<img class="thumbnail" src="imagesFile/RedPacketListImg.jpg" width="300" height="90">
<h3 class="text_line">第二行</h3>
<img class="thumbnail" src="imagesFile/RedPacketListImg.jpg" width="300" height="90">
<img class="thumbnail" src="imagesFile/RedPacketListImg.jpg" width="300" height="80">
<img class="thumbnail" src="imagesFile/RedPacketListImg.jpg" width="300" height="90">
<img class="thumbnail" src="imagesFile/RedPacketListImg.jpg" width="300" height="90">
//CSS
img
{
    float:right;
}
/*彼此相邻的浮动元素：*/
.thumbnail
{
    float:left;
    width:110px;
    height:90px;
    margin:5px;
}
.text_line
{
    clear:both;
    margin-bottom:2px;
}
```

## CSS 水平对齐(Horizontal Align)

关于 CSS 中元素的水平对齐(Horizontal Align)，你可以使用多种属性来进行设置。

(1)块元素对齐。块元素是一个元素，占用了全宽，前后都是换行符。块元素有h1元素、p元素、div元素。

(2)中心对齐,使用margin属性。块元素可以把左，右页边距设置为"自动"对齐。需要注意的是，在IE8中使用margin:auto属性无法正常工作，除非声明 !DOCTYPE。
IE5中块元素有一个margin处理BUG。为了使上述例子能工作，在IE5中，需要添加一些额外的代码。

```
<div class="center">
    <p>In my younger and more vulnerable years my father gave me some advice that I've been turning over in my mind ever since.</p>
    <p>'Whenever you feel like criticizing anyone,' he told me, 'just remember that all the people in this world haven't had the advantages that you've had.'</p>
</div>
<p><b>注意: </b>使用 margin:auto无法兼容 IE8, 除非!DOCTYPE已经声明.</p>

//CSS
.center
{
    margin:auto;
    /*如果宽度是100％，对齐是没有效果的。*/
    width:70%;
    background-color:#b0e0e6;
}
```

(3)使用position属性设置左，右对齐。元素对齐的方法之一是使用绝对定位。需要注意的是绝对定位与文档流无关，所以它们可以覆盖页面上的其它元素。

```
<div class="right">
    <p>In my younger and more vulnerable years my father gave me some advice that I've been turning over in my mind ever since.</p>
    <p>'Whenever you feel like criticizing anyone,' he told me, 'just remember that all the people in this world haven't had the advantages that you've had.'</p>
</div>
//CSS
{
    position:absolute;
    right:0px;
    width:300px;
    background-color:#b0e0e6;
}
```

(4)使用float属性设置左，右对齐。

```
.right {
    float: right;
    width: 300px;
    background-color: darkseagreen;
}
```

(5)使用Padding设置垂直居中对齐。

```
.center {
    /*简单的设置垂直居中对齐的方式就是头部顶部使用 padding*/
    padding: 70px 0;
    border: 3px solid purple;
    /*如果要水平和垂直都居中，可以使用 padding 和 text-align: center*/
    text-align: center;
}
```
