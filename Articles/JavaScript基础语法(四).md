## JavaScript 对象

JavaScript 中的所有事物都是对象：字符串、数值、数组、布尔型的值、日期、数学和正则表达式、函数...此外，JavaScript 允许自定义对象。

对象只是一种特殊的数据。对象拥有属性和方法。

**访问对象的属性**

访问对象属性的语法是：

```
objectName.propertyName
```

这个例子使用了 String 对象的 length 属性来获得字符串的长度：

```
var message="Hello World!";
 var x=message.length;
```

**访问对象的方法**

方法是能够在对象上执行的动作。通过以下语法来调用方法：

```
objectName.methodName() 
```

这个例子使用了 String 对象的 toUpperCase() 方法来将文本转换为大写：

```
var message="Hello world!";
var x=message.toUpperCase();
```

**创建 JavaScript 对象**

通过 JavaScript，您能够定义并创建自己的对象。

创建新对象有两种不同的方法：

* 定义并创建对象的实例；

这个例子创建了对象的一个新实例，并向其添加了四个属性：

```
person=new Object();
person.firstname="John";
person.lastname="Doe";
person.age=50;
person.eyecolor="blue";
```

替代语法（使用对象 literals）：

```
person={firstname:"John",lastname:"Doe",age:50,eyecolor:"blue"};
```

* 使用函数来定义对象(对象构造器)，然后创建新的对象实例。

```
<body>

<script>
//在JavaScript中，this通常指向的是我们正在执行的函数本身，或者是指向该函数所属的对象（运行时）。
function person(firstname,lastname,age,eyecolor){
	this.firstname=firstname;
	this.lastname=lastname;
	this.age=age;
    this.eyecolor=eyecolor;
}
myFather=new person("John","Doe",50,"blue");
document.write(myFather.firstname + " is " + myFather.age + " years old.");
</script>

</body>
```






