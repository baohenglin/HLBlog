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

### LESS、SASS 预处理器

使用 LESS、SASS 等预处理器可以加快 css 开发效率。可以减少在书写样式时对权重的思考，使 CSS 更易维护和扩展。

* LESS
* SASS

sass和less主要区别:在于二者的实现方式不同。less 是基于 JavaScript 的，在客户端处理，所以安装的时候用 npm。sass 是基于 ruby，所以在服务器处理。



