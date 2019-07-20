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

font-size 属性设置文本的大小。能否管理文字的大小，在网页设计中是非常重要的。但是，你不能通过调整字体大小使段落看上去像标题，或者使标题看上去像段落。请务必使用正确的HTML标签，就<h1> - <h6>表示标题和<p>表示段落。字体大小的值可以是绝对或相对的大小。

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
  
