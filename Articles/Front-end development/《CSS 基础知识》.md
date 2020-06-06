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

### 元素继承权重

继承没有权重，也就是 NULL。



