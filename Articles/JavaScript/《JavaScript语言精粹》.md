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

# 第4章 函数

JavaScript 设计得最出色的就是它的函数的实现。它几乎接近完美。但是，JavaScript 的函数也存在瑕疵。函数包含一组语句，它们是 JavaScript 的基础模块单元，用于代码复用、信息隐藏和组合调用。函数用于指定对象的行为。一般来说，所谓编程，就是将一组需求分解成一组函数与数据结构的技能。

## 函数对象

JavaScript 中的函数就是对象。对象是 “键值对”的集合并拥有一个连接到原型对象的隐藏连接。对象字面量产生的对象连接到 Object.prototype。函数对象连接到 Function.prototype（该原型对象本身连接到 Object.prototype）。每个函数在创建时会附加两个隐藏属性：函数的上下文和实现函数行为的代码。

每个函数对象在创建时也随配有一个 prototype 属性。它的值是一个拥有 constructor 属性且值即为该函数的对象。这和隐藏连接到 Function.prototype 完全不同。

因为函数是对象，所以它们可以像任何其他的值一样被使用。函数可以保存在变量、对象和数组中。函数还可以被当做参数传递给其他函数，函数也可以再返回函数。而且，因为函数是对象，所以函数可以拥有方法。

## 函数字面量

函数对象通过函数字面量来创建：

```
var add = function (a, b) {
  return a + b;
}
```

函数字面量包括4个部分。第1部分是关键字 function。

第2部分是函数名，它可以被省略。函数可以用它的名字来递归地调用自己。此名字也能被调试器和开发工具用来识别函数。如果没有给函数命名，比如上面这个例子，它被称为匿名函数（aninymous）。

函数的第3部分是包围在圆括号中的一组参数。多个参数用逗号分隔。这些参数的名称将被定为为函数中的变量。它们不像普通的变量那样将被初始化为 undefined，而是在该函数被调用时初始化为实际提供的参数的值。

函数的第4部分是包围在花括号中的一组语句。这些语句是函数的主体，它们在函数被调用时执行。

函数字面量可以出现在任何允许表达式出现的地方。函数也可以被定义在其他函数中。一个内部函数除了可以访问自己的参数和变量，同时它也能自由访问把它嵌套在其中的父函数的参数与变量。通过函数字面量创建的函数对象包含一个连到外部上下文的连接。这被称为闭包（closure）。它是 JavaScript 强大表现力的来源。

## 函数调用

调用一个函数会暂停当前函数的执行，传递控制权和参数给新函数。除了声明时定义的形式参数，每个函数还接收两个附加的参数：this 和 arguments。参数 this 在面向对象编程中非常重要，它的值取决于调用的模式。在 JavaScript中一共有4种调用模式：**方法调用模式、函数调用模式、构造器调用模式和 apply调用模式**。这些模式在如何初始化关键参数 this 上存在差异。

**调用运算符**是跟在任何产生一个函数值的表达式之后的一对圆括号。圆括号内可包含零个或多个用逗号隔开的表达式。每个表达式产生一个参数值。每个参数值被赋予函数声明时定义的形式参数名。当实际参数（arguments）的个数与形式参数（parameters）的个数不匹配时，不会导致运行时错误。如果实际参数值过多了，超出的参数值会被忽略。如果实际参数值过少，缺失的值会被替换为 undefined。对参数值不会进行类型检查：任何类型的值都可以被传递给任何参数。

## 方法调用模式

当一个函数被保存为对象的一个属性时，我们称它为一个方法。当一个方法被调用时，this 被绑定到该对象。如果调用表达式包含一个提取属性的动作（即包含一个.点表达式或[subscript]下标表达式），那么它就是被当做一个方法来调用。

```
//创建 myObject对象。它有一个 value属性和一个 increment 方法。
//increment方法接受一个可选的参数。如果参数不是数字，那么默认使用数字1.
var myObject = {
  value: 0,
  increment: function (inc) {
    this.value += typeof inc === 'number' ? inc : 1;
  }
};
myObject.increment();
document.write(myObject.value); //1

myObject.increment(2);
document.write(myObject.value); //3
```

方法可以使用 this 访问自己所属的对象，所以它能从对象中取值或对对象进行修改。this到对象的绑定发生在调用的时候。这个“超级”延迟绑定(very late binding)使得函数可以对 this 高度复用。通过 this 可取得它们所属对象的上下文的方法称为公共方法（public method）。

## 函数调用模式

当一个函数并非一个对象属性时，那么它就是被当做一个函数来调用的：

```
var sum = add(3, 4);
```

以此模式调用函数时，this被绑定到全局对象。这是语言设计上的一个错误。倘若语言设计正确，那么当内部函数被调用时，this 应该仍然绑定到外部函数的 this 变量。这个设计错误的后果就是方法不能利用内部函数来帮助它工作，因为内部函数的 this 被绑定了错误的值，所以不能共享该方法对对象的访问权。幸运的是，有一个很容易的解决方案：如果该方法定义一个变量并给它赋值为 this，那么内部函数就可以通过那个变量访问到 this。按照约定，把那个变量命名为 that：

```
//给 myObject 增加一个 double 方法。
myObject.double = function () {
  var that = this;
  var helper = function () {
    that.value = add(that.value, that.value);
  }
  helper(); //以函数的形式调用 helper
};
// 以方法的形式调用 double。
myObject.double();
document.write(myObject.value); //6
```

## 构造器调用模式

JavaScript 是一门基于原型继承的语言。这意味着对象可以直接从其他对象继承属性。该语言是无类型的。这偏离了当今编程语言的主流风格。当今大多数语言都是基于类的语言。尽管原型继承极富表现力，但它并未被广泛理解。JavaScript本身对它原型的本质也缺乏信心，所以它提供了一套和基于类的语言类似的对象构建语法。有类型化语言编程经验的程序员很少有愿意接受原型继承的，并且认为借鉴类型化语言的语法模糊了这门语言真实的原型本质。真是两边不讨好。

如果在一个函数前面带上 new 来调用，那么背地里将会创建一个连接到该函数的 prototype 成员的新对象，同时 this 会被绑定到那个新对象上。

```
//创建一个名为 Quo 的构造器函数，它构造一个带有 status 属性的对象。
var Quo = function (string) {
  this.status = stirng;
};
// 给 Quo 的所有实例提供一个名为 get_status 的公共方法。
Quo.prototype.get_status = function () {
  return this.status;
};
// 构造一个 Quo 实例。
var myQuo = new Quo("confused");
document.write(myQuo.get_status());
```

一个函数，如果创建的目的就是希望结合 new 前缀来调用，那它就被称为构造器函数。按照约定，它们保存在以大写格式命名的变量里。如果调用构造器函数时没有在前面加上 new，可能会发生非常糟糕的事情，既没有编译时警告，也没有运行时警告，所以大写约定非常重要。

不推荐使用这种形式的构造器函数。

## Apply调用模式

因为 JavaScript 是一门函数式的面向对象编程语言，所以函数可以拥有方法。apply方法让我们构建一个参数组传递给调用函数。它也允许我们选择 this 的值。apply方法接收两个参数，第1个是要绑定给 this 的值，第2个就是一个参数数组。

```
// 构造一个包含两个数字的数组，并将它们相加。
var array = [3, 4];
var sum = add.apply(null, array); // sum值为7。
// 构造一个包含 status 成员的对象。
var statusObject = {
  status: 'A-OK'
};
// statusObject 并没有继承自 Quo.prototype，但我们可以在 statusObject 上调
// 用 get_status 方法，尽管 statusObject 并没有一个名为 get_status 的方法。

var status = Quo.prototype.get_status.apply(statusObject);
```
#### 2019.11.25

## 参数

当函数被调用时，会得到一个“免费”配送的参数，那就是 arguments 数组。函数可以通过此参数访问所有它被调用时传递给它的参数列表，包括那些没有被分配给函数声明时定义的形式参数的多余参数。这使得编写一个无须指定参数个数的函数成为可能：

```
// 构造一个将大量的值相加的函数。
// 注意该函数内部定义的变量 sum 不会与函数外部定义的 sum 产生冲突。
// 该函数只会看到内部的那个变量。

var sum = function () {
  var i, sum = 0;
  for (i = 0; i < arguments.lenght; i +=1) {
    sum += arguments[i];
  }
  return sum;
};
document.write(sum(4, 8, 15, 16, 23, 42));// 108
```

因为语言的一个设计错误，arguments 并不是一个真正的数组。它只是一个“类似数组(array-like)”的对象。arguments 拥有一个 length 属性，但它没有任何数组的方法。

P45





