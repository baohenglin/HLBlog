## JavaScript 函数

JavaScript 使用关键字 function 定义函数。函数声明后不会立即执行，会在我们需要的时候调用到。

```
function functionName(parameters) {        
  执行的代码        
}
```

函数除了可以通过声明定义，也可以是一个表达式。

函数表达式可以存储在变量中：

```
<body>

<p>函数可以存储在变量中:</p>
<p id="demo"></p>
<script>
var x = function (a, b) {return a * b};
document.getElementById("demo").innerHTML = x;
</script>

</body>
```

在函数表达式存储在变量后，变量也可作为一个函数使用：

```
<body>
<p>函数存储在变量后，变量可作为函数使用：</p>
<p id="demo"></p>
<script>
var x = function (a, b) {return a * b};
document.getElementById("demo").innerHTML = x(4, 3);
</script>
</body>
```

