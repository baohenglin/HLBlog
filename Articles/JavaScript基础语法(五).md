## JavaScript Array（数组）对象

[JavaScript Array 对象](https://www.w3cschool.cn/jsref/jsref-obj-array.html)

数组对象的作用是：使用单独的变量名来存储一系列的值。创建数组有三种方式：

* (1)常规方式

```
var mycars = new Array();
mycars[0] = "Saab";
mycars[1] = "Volvo";
mycars[2] = "BMW";
```

* (2)简洁方式：

```
var myCars=new Array("Saab","Volvo","BMW");
```

* (3)字面的方式

```
var myCars=["Saab","Volvo","BMW"];
```

**访问数组**：通过指定数组名以及索引号码，你可以访问某个特定的元素。

访问myCars数组的第一个值。

```
var name = myCars[0];
```

以下实例修改了数组myCars的第一个元素：

```
myCars[0]="Opel";
```

在一个数组中你可以有不同的对象。所有的JavaScript变量都是对象。数组元素是对象。函数是对象。因此你可以在数组中有不同的变量类型。你可以在一个数组中包含对象元素、函数、数组。

```
myArray[0]=Date.now;
myArray[1]=myFunction;
myArray[2]=myCars; 
```

**数组方法和属性**：使用数组对象预定义属性和方法。

```
var x=myCars.length             // the number of elements in myCars
var y=myCars.indexOf("Volvo")   // the index position of "Volvo"
```











