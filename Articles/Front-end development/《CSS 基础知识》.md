# CSS 

## CSS 权重

### 权重规则：

* 通配符的权重是 0
* 标签、伪元素的权重是 0001
* class、类属性值二者的权重是 0010
* id 的权重是 0100
* 行内样式的权重是 1000

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>FQY</title>
  <style>
    div {
      color: red;
    }
    #hdcms {
      color: purple
    }
    .fengqingyang {
      color: green;
    }
  </style> 
</head>

<body>
  <div class="fengqingyang" id="hdcms">BHL</div>
</body>
```

### 继承规则

子元素可以继承父元素设置的样式。但是需要注意以下两点：

* 子元素并不能继承父元素的全部样式。比如边框、高度等并不会继承
* 继承没有权重，也就是 NULL。

### 通配符

在开发中同时存在使用 “*” 选择器和继承时，会显示通配符的样式。因为**通配符的权重为 0，而继承的规则没有权重**。

```
<style>
  * {
  	color: red;
  }

  h2 {
  	color: blue;
  }
</style>

<article>
	<h2>hdcms <span>内容管理系统</span></h2>
</article>
```

h2 中的span并没有继承 h2 的颜色，就是因为继承没有权重。而使用了 * 权重为0的规则。

### LESS、SASS 预处理器

使用 LESS、SASS 等预处理器可以加快 css 开发效率。可以减少在书写样式时对权重的思考，使 CSS 更易维护和扩展。

* LESS
* SASS

sass和less主要区别:在于二者的实现方式不同。less 是基于 JavaScript 的，在客户端处理，所以安装的时候用 npm。sass 是基于 ruby，所以在服务器处理。

## 文本控制

### 字体设置

可以定义多个字体，系统会依次查找。比如 'Courier New'字体不存在将使用 Courier 以此类推。

```
font-family: 'Courier New', Courier, monospace;
```

#### 自定义字体

可以声明自定义字段，如果客户端不存在将下载该字体，使用方式也是通过 font-family 引入。

```
<style>
  @font-face {
  	font-family: "houdunren";
  	src: 	url("SourceHanSansSC-Light.otf") format("opentype"),
  				url("SourceHanSansSC-Heavy.otf") format("opentype");
  }

  span {
  	font-family: 'houdunren';
  }
</style>
```

### 字重定义

字重指字的粗细定义。font-weight 的取值范围 normal | bold | bolder | lighter | 100 ~900。其中 400 对应 normal，700 对应 bold，一般情况下使用 bold 或 normal 的较多。

```
<style>
span {
	font-weight: bold;
}

strong:last-child {
	font-weight: normal;
}
</style>
...

<span>houdunren.com</span>
<strong>hdcms.com</strong>
```

### 文本字号

字号用于控制字符的显示大小，包括 xx-small | x-small | small | meidum | large | x-large | xx-large。

#### 百分数

百分数是子元素相对于父元素的大小。如果父元素是 20px，子元素设置为 200%，那么子元素字体大小为父元素字体大小的 2 倍。

```
<style>
  article {
  	font-size: 20px;
  }

  span {
  	font-size: 500%;
  }
</style>
...

<article>
	<span>houdunren.com</span>
</article>
```

#### em

em单位等同于百分数单位。

```
<style>
  article {
  	font-size: 20px;
  }

  span {
  	font-size: 5em;
  }
</style>
...

<article>
	<span>houdunren.com</span>
</article>
```

### 文本颜色

#### 字符串颜色

```
color:red;
```

#### 十六进制网页颜色

如果颜色字符相同，比如 #dddddd，可以简写为 #ddd

```
color:#ddffde
```

#### 使用 RGB 颜色

```
color:rgb(38, 149, 162);
```

#### 透明颜色

透明色可以 0~1 之间的值。0 表示透明，1 表示不透明。

```
color:rgba(38, 149, 162, .2);
```

### 行高定义

```
div {
   line-height: 2em;
}
```

### 倾斜风格：控制字符的倾斜样式

```
<style>
  span {
  	font-style: italic;
  }

  em {
  	font-style: normal;
  }
</style>
...

<span>houdunren.com</span>
<hr>
<em>hdcms.com</em>
```

### 组合定义

可以使用 font 一次将字符样式定义，但要注意以下两点：

* 必须有字体规则；
* 必须有字符大小规则。

```
span {
   font: bold italic 20px/1.5 'Courier New', Courier,monospace;
}
```

上例中的 20px 为字体大小，1.5 为 1.5 倍行高定义。

## 文本样式

### 大小转换

```
span {
    /* 小号大写字母 */
   font-variant: small-caps;
}
h2 {
  /* 首字母大写 */
  text-transform: capitalize;

  /* 全部大写 */
  text-transform: uppercase;

  /* 全部小写 */
  text-transform: lowercase;
}
```

### 文本线条

#### 添加隐藏删除线、下换线、删除线、上划线等效果

```
<style>
    a {
      /* 隐藏删除线 */
      text-decoration: none;
    }
    .underline {
      /* 下划线 */
      text-decoration: underline;
    }
    .through {
      text-decoration: line-through;
    }
    .overline {
      /* 上划线 */
      text-decoration: overline;
    }
</style>

<body>
  <a href="">google.com</a>
  <hr>
  <span class="underline">下划线</span>
  <hr>
  <span class="through">删除线</span>
  <hr>
  <span class="overline">上划线</span>
</body>
```

#### 阴影控制

参数顺序为：颜色、水平偏移、垂直偏移、模糊度。

```
<style>
  h2 {
  	text-shadow: rgba(13, 6, 89, 0.8) 3px 3px 5px;
  }
</style>
...

<h2>houdunren.com</h2>
```

#### 空白处理

使用 white-space 控制文本空白显示。







