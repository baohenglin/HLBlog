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

* (1)定义并创建对象的实例；

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

* (2)使用函数来定义对象(对象构造器)，然后创建新的对象实例。

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

**创建 JavaScript 对象实例**

一旦您有了对象构造器，就可以创建新的对象实例，如下所示：

```
var myFather=new person("John","Doe",50,"blue");
var myMother=new person("Sally","Rally",48,"green");
```

**把属性添加到 JavaScript 对象**

您可以通过为对象赋值，向已有对象添加新属性。假设 personObj 已存在 - 您可以为其添加这些新属性：firstname、lastname、age 以及 eyecolor：

```
person.firstname="John";
 person.lastname="Doe";
 person.age=30;
 person.eyecolor="blue";

 x=person.firstname;
```

**把方法添加到 JavaScript 对象**

方法只不过是附加在对象上的函数。在构造器函数内部定义对象的方法：

```
function person(firstname,lastname,age,eyecolor)
 {
     this.firstname=firstname;
     this.lastname=lastname;
     this.age=age;
     this.eyecolor=eyecolor;

     this.changeName=changeName;
     function changeName(name)
     {
           this.lastname=name;
     }
 }
```

**JavaScript prototype**

JavaScript 是面向对象的语言，但 JavaScript 不使用类。在 JavaScript 中，不会创建类，也不会通过类来创建对象。JavaScript 基于 prototype，而不是基于类的。

**JavaScript for...in 循环**

JavaScript for...in 语句循环遍历对象的属性。

```
for (variable in object)
{
   code to be executed
}
```

示例1：

```
<body>
	
<p>点击下面的按钮，循环遍历对象 "person" 的属性。</p>
<button onclick="myFunction()">点击这里</button>
<p id="demo"></p>
<script>
function myFunction(){
	var x;
	var txt="";
	var person={fname:"Bill",lname:"Gates",age:56}; 
	for (x in person){
		txt=txt + person[x];
	}
	document.getElementById("demo").innerHTML=txt;
}
</script>
	
</body>
```

下面给出一个完整简易的dictionary实现。注意其外部使用了function，内部使用了Object对象的动态添加属性的一些特性。

```
<!DOCTYE html>
<html> 	
	<head> 	 	
		<script> 	 	
			function dictionary(){ 	 	
				//Define an object for a dictionary type 	 	
				var _obj = new Object(); 	 	
				//Check whether a certain key contained or not 	 	
				this.containsKey = function(k){ 	 	
					var isContained = false; 	 	
					for(var attr in _obj){ 	 	
						if(attr == k){ 	 	
							isContained = true; 	 	
							break; 	 	
						} 	 	
					} 	 	
					return isContained; 	 	
				} 	 	
				//Add a new element 	 	
				this.addElement = function(k,v){ 	 	
					if(!this.containsKey(k)){ 	 	
						_obj[k] = v; 	 	
					} 	 	
				} 	 	
				//Remove an existing element 	 	
				this.removeElement = function(k){ 	 	
					if(this.containsKey(k)){ 	 	
						delete _obj[k]; 	 	
					} 	 	
				} 	 	
				//Print the result 	 	
				this.printAll = function(){ 	 	
					var result = JSON.stringify(_obj); 	 	
					return result; 	 	
				} 	 	
			} 	 	
			var newD = new dictionary(); 	 	
			function AddAndPrint(){ 	 	
				newD.addElement("0000","张三"); 	 	
				newD.addElement("0001","李四"); 	 	
				document.getElementById("divContent").innerHTML = newD.printAll(); 	 	
			} 	 	
			function RemoveAndPrint(k){ 	 	
				newD.removeElement("0000"); 	 	
				document.getElementById("divContent").innerHTML = newD.printAll(); 	 	
			} 	 	
		</script> 	 	
	</head> 	 	
	<body> 	 	
		<div id="divContent"></div> 	 	
		<input type="button" value="Click Me To Add" onclick="AddAndPrint();"/> 	 	
		<input type="button" value="Click Me To Remove" onclick="RemoveAndPrint(&#39;0000&#39;);"/> 	 	
	</body> 	 	
</html>
```




