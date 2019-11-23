## JavaScript语言精粹

JavaScript是一门重要的语言，因为它是 Web 浏览器的语言。它与浏览器的结合使它成为世界上最流行的编程语言之一。JavaScript 建立在一些非常优秀的想法和少数非常糟糕的想法之上。那些优秀的想法包括**函数、弱类型、动态对象和富有表现力的对象字面量表示法**。那些糟糕的想法包括基于全局变量的编程模型。

现今大部分编程语言中都流行要求强类型。其原理在于强类型允许编译器在编译时检测错误。我们能越早检测和修复错误，付出的代价就越小。 JavaScript 是一门弱类型的语言，所以 JavaScript 编译器不能检测出类型错误。这可能让从强类型语言转向 JavaScript 的开发人员感到恐慌。但事实证明，强类型并不会让你的测试工作变得轻松。而且我在工作中发现，强类型检查找到的错误并不是令我头痛的错误。

#### 2019.11.23 周六 多云

## 标识符

标识符由一个字母开头，其后可选择性地加上一个或多个字母、数字或下划线。标识符不能使用下面这些保留字：

```
abstract  boolean break byte  case
catch char  class const continue
debugger  default delete  do  double
else  enum  export  extends false
final finally float for function
goto  if  implements  import  in
instanceof  int interface long  native
new null  package private protected 
public  return  short static  super 
switch  synchronized  this  throw throws
transient true  try typeof  var
volatile  void  while with
```

JavaScript 不允许使用保留字来命名变量或参数。此外，JavaScript 不允许在对象字面量中，或者用点运算符提取对象属性时，使用保留字作为对象的属性名。

标识符被用于语句、变量、参数、属性名、运算符和标记。

## 数字

JavaScript只有一个数字类型。它在内部被表示为64位的浮点数，和 Java的 double 数字类型一样。与其他大多数编程语言不同的是，它没有分离出整数类型，所以1和1.0的值相同。这提供了很大的方便，因为它完全避免了短整型的溢出问题。

如何一个数字字面量有指数部分，那么这个字面量的值等于e之前的数字与10的e之后数字的次方相乘。所以200 和 2e2是相同的数字。

NaN是一个数值，它表示一个不能产生正常结果的运算结果。NaN不等于任何值，包括它自己。你可以用函数 isNaN(number)检测 NAN。

Infinity表示所有大于 1.79769313486231570e+308

数字拥有方法。JavaScript有一个对象 Math，它包含一套作用于数字的方法。例如，可以用 Math.floor(number)方法把一个数字转换成一个整数。

## 字符串

字符串字面量可以被包在一对单引号或双引号中，它可能包含0个或多个字符。\(反斜线符号)是转义字符。JavaScript 在被创建的时候，Unicode是一个16位的字符集，所以 JavaScript中的所有字符都是16位的。

JavaScript 没有字符类型。要表示一个字符，只需创建仅包含一个字符的字符串即可。

转义字符用来把那些正常情况下不被允许的字符插入到字符串中，比如反斜线、引号和控制字符。\u约定用来指定数字字符编码。比如 "A" === "\u0041"

字符串有一个 length属性。例如，"seven".length是5。

字符串是不可变的。一旦字符串被创建，就永远无法改变它。但你可以很容易地通过 ‘+’ 运算符连接其他字符串来创建一个新的字符串。两个包含着完全相同的字符且字符顺序也相同的字符串被认为是相同的字符串。所以：

```
'c' + 'a' + 't' === 'cat'  是true
```

```
'cat'.toUpperCase() === 'CAT'
```

## 语句

一个编译单元包含一组可执行的语句。在 Web浏览器中，每个 <script>标签提供一个被编译且立即执行的编译单元。因为缺少连接器(链接器Linker是编程语言或操作系统提供的工具，它的工作就是解析未定义的符号引用，将目标文件中的占位符替换为符号地址)，JavaScript 把它们一起抛到一个公共的全局名字空间中。
  
当 var 语句被用在函数内部时，它定义的是这个函数的私有变量。

switch、while、for和 do 语句允许有一个可选的前置标签（label），它配合 break 语句来使用。语句通常按照从上到下的顺序执行。JavaScript 可以通过条件语句（if 和 switch）、循环语句（while、for 和 do）、强制跳转语句（break、return 和 throw）和函数调用来改变执行序列。

代码块是包在一对花括号中的一组语句。不像许多其他语言，JavaScript中的代码块不会创建新的作用域，因此变量应该被定义在函数的头部，而不是定义在代码块中。

if 语句根据表达式的值改变程序流程。表达式的值为真时执行跟在其后的代码块，否则，执行可选的 else 分支。

下面列出的值被当做假：

* false
* null
* undefined
* 空字符串 ' '
* 数字 0
* 数字 NaN

其他所有的值都被当做真，包括 true、字符串“false”，以及所有的对象。

for in 语句会枚举一个对象的所有属性名（或键名）。在每次循环中，object的下一个属性名字符串被赋值给 variable。通常你需要检测 object.hasOwnProperty(variable)来确定这个属性名是该对象的成员还是来自原型链。

```
for (myvar in obj) {
  if (obj.hasOwnProperty(myvar)) {
    ...
  }
}
```

```
try {

} catch(varName) {

}
```

try 语句执行一个代码块，并捕获该代码块抛出的任何异常。catch从句定义一个新的变量 variable 来接收抛出的异常对象。

```
throw expression;
```

throw 语句抛出一个异常。如果 throw 语句在一个 try 代码块中，那么控制流会跳转到 catch从句中。如果 throw 语句在函数中，则该函数调用被放弃，控制流跳转到调用该函数的 try 语句的 catch 从句中。throw 语句中的表达式通常是一个对象字面量，它包含一个 name属性和一个 message 属性。异常捕获器可以使用这些信息去决定该做什么。

```
return expression;
```
return 语句会导致从函数中提前返回。它也可以指定要被返回的值。如果没有指定返回表达式，那么返回值是 undefined。

```
break ;
```

break 语句会使程序退出一个循环语句或 switch 语句。它可以指定一个可选的标签，那退出的就是带该标签的语句。




