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
 
        color           设置文本颜色
        direction       设置文本方向
        letter-spacing  设置字符间距
        line-height     设置行高
        text-align      对齐元素中的文本
        text-decoration 向文本添加修饰
        text-indent     缩进元素中文本的首行
        text-shadow     设置文本阴影
        text-transform  控制元素中的字母
        unicode-bidi    设置或返回文本是否被重写
        vertical-align  设置元素的垂直对齐
        white-space     设置元素中空白的处理方式
        word-spacing    设置字间距

