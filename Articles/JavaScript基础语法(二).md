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

以上函数实际上是一个 匿名函数 (函数没有名称)。函数存储在变量中，不需要函数名称，通常通过变量名来调用。

**Function() 构造函数**

函数除了通过关键字 function 定义，函数同样可以通过内置的 JavaScript 函数构造器（Function()）定义。

```
<body>
<p>JavaScrip 内置构造函数。</p>
<p id="demo"></p>
<script>
var myFunction = new Function("a", "b", "return a * b");
document.getElementById("demo").innerHTML = myFunction(4, 3);
</script>
</body>
```

实际上，不必使用构造函数。上面实例可以写成：

```
<body>
<p id="demo"></p>
<script>
var myFunction = function (a, b) {return a * b}
document.getElementById("demo").innerHTML = myFunction(4, 3);
</script>
</body>
```

【注意】在 JavaScript 中，很多时候，你需要避免使用 new 关键字。

**函数提升（Hoisting）**

提升（Hoisting）是 JavaScript 默认将当前作用域提升到前面去的的行为。提升（Hoisting）应用在变量的声明与函数的声明。因此，函数可以在声明之前调用：

```
myFunction(5);               
function myFunction(y) {        
    return y * y;        
}
```

【注意】使用表达式定义函数时无法提升。

**自调用函数**

函数表达式可以 "自调用"。自调用表达式会自动调用。如果表达式后面紧跟 () ，则会自动调用。

```
<body>

<p>函数可以自动调用：</p>
<p id="demo"></p>
<script>
(function () {
    document.getElementById("demo").innerHTML = "Hello! 我是自己调用的";
})();
</script>

</body>
```

以上函数实际上是一个 匿名自我调用的函数 (没有函数名)。

**函数可以作为一个值使用**

JavaScript函数可以作为一个值使用：

```
<body>
<p>函数可作为一个值：</p>
<p>x = myFunction(4,3) 或 x = 12</p>
<p>两种情况下，x 的值都为 12。</p>
<p id="demo"></p>
<script>
function myFunction(a, b) {
    return a * b;
}
var x = myFunction(4, 3);
document.getElementById("demo").innerHTML = x;
</script>
</body>
```

JavaScript 函数可作为表达式使用：

```
<body>

<p>函数可作为一个表达式使用。</p>
<p id="demo"></p>
<script>
function myFunction(a, b) {
    return a * b;
}
var x = myFunction(4, 3) * 2;
document.getElementById("demo").innerHTML = x;
</script>

</body>
```

**函数是对象**

在 JavaScript 中使用 typeof 操作符判断函数类型将返回 "function" 。但是，将JavaScript 函数描述为一个对象更加准确。JavaScript 函数有 属性 和 方法。arguments.length 属性返回函数调用过程接收到的参数个数：

```
<body>
<p> arguments.length 属性返回函数接收到参数的个数：</p>
<p id="demo"></p>
<script>
function myFunction(a, b) {
    return arguments.length;
}
document.getElementById("demo").innerHTML = myFunction(4, 3);
</script>
</body>
```

toString() 方法将函数作为一个字符串返回:

```
<body>
<p> toString() 将函数作为一个字符串返回：</p>
<p id="demo"></p>
<script>
function myFunction(a, b) {
    return a * b;
}
document.getElementById("demo").innerHTML = myFunction(8,8).toString();
</script>
</body>
```

函数定义作为对象的属性，称之为对象方法。 函数如果用于创建新的对象，称之为对象的构造函数。


## JavaScript 函数参数

JavaScript 函数对参数的值(arguments)没有进行任何的检查。JavaScript 函数参数与大多数其他语言的函数参数的区别在于：它不会关注有多少个参数被传递，不关注传递的参数的数据类型。

**函数显式参数与隐藏参数(arguments)**

函数的显式参数：函数显式参数在函数定义时列出。函数隐藏参数(arguments)在函数调用时传递给函数真正的值。

```
function functionName(parameter1, parameter2, parameter3) {
    code to be executed
}
```

**参数规则**:

```
JavaScript 函数定义时参数没有指定数据类型。
JavaScript 函数对隐藏参数(arguments)没有进行检测。
JavaScript 函数对隐藏参数(arguments)的个数没有进行检测。
```

如果函数在调用时缺少参数，参数会默认设置为： undefined。有时这是可以接受的，但是建议最好为参数设置一个默认值。

```
<body>

<p>设置参数的默认值。</p>
<p id="demo"></p>
<script>
function myFunction(x, y) {
    if (y === undefined) {
        y = 0;
    }    
    return x * y;
}
document.getElementById("demo").innerHTML = myFunction(4);
</script>

</body>
```

如果函数调用时设置了过多的参数，参数将无法被引用，因为无法找到对应的参数名。 只能使用 arguments 对象来调用。

**Arguments 对象**

JavaScript 函数有个内置的对象 arguments 对象。argument 对象包含了函数调用的参数数组。通过这种方式你可以很方便的找到每一个index参数的值。

```
<body>
<p>查找最大的数。</p>
<p id="demo"></p>
<script>
x = findMax(1, 123, 500, 115, 44, 88);

function findMax() {
    var i, max = arguments[0];

    if(arguments.length < 2)return max;

    for (i = 0; i < arguments.length; i++) {
        if (arguments[i] > max) {
            max = arguments[i];
        }
    }
    return max;
}
document.getElementById("demo").innerHTML = x;
</script>
</body>
```

创建一个函数用来统计所有数值的和：

```
<body>
<p>计算所有参数之和：</p>
<p id="demo"></p>
<script>
function sumAll() {
    var i, sum = 0;
    for(i = 0; i < arguments.length; i++) {
        sum += arguments[i];
    }
    return sum;
} 
document.getElementById("demo").innerHTML =
	sumAll(1, 123, 500, 115, 44, 88);
</script>
</body>
```






