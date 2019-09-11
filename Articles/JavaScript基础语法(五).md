## JavaScript Array（数组）对象

[JavaScript Array 对象](https://www.w3cschool.cn/jsref/jsref-obj-array.html)

数组对象的作用是：使用单独的变量名来存储一系列的值。创建数组有以下三种方式：

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

**合并两个数组**

```
var hege = ["Cecilie", "Lone"];
var stale = ["Emil", "Tobias", "Linus"];
var children = hege.concat(stale);
```

合并三个数组：

```
var parents = ["Jani", "Tove"];
var brothers = ["Stale", "Kai Jim", "Borge"];
var children = ["Cecilie", "Lone"];
var family = parents.concat(brothers, children);
```

**删除数组的最后一个元素**

```
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.pop();
```

**在数组末尾添加一个新元素**

```
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.push("Kiwi")
```

**数组反转排序**

```
fruits.reverse();
```

**删除数组第一个元素**

```
fruits.shift();
```

**截取下标1到2的元素**

```
var fruits = ["Banana", "Orange", "Lemon", "Apple", "Mango"];
var citrus = fruits.slice(1,3);
```

**数组元素按照字母升序排列**

```
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.sort();
```

**数组元素按照数字字母升序**

```
var points = [40,100,1,5,25,10];
points.sort(function(a,b){return a-b});
```
  
## JavaScript Boolean（布尔）对象

创建 Boolean 对象。下面的代码定义了一个名为 myBoolean 的布尔对象：

```
var myBoolean=new Boolean();
```
如果布尔对象无初始值或者其值为:0、-0、null、""、false、undefined、NaN，那么对象的值为false，否则，其值为true。

## JavaScript Math（算数）对象

Math（算数）对象的作用是：执行常见的算数任务。

**Math 对象**：Math 对象提供多种算数值类型和函数。无需在使用这个对象之前对它进行定义。Math 对象不能使用 new 关键字创建对象实例。直接用 “对象名.成员”的格式来访问其属性或者方法。

**算数值**

JavaScript 提供 8 种可被 Math 对象访问的算数值：

```
 Math.E
 Math.PI
 Math.SQRT2
 Math.SQRT1_2
 Math.LN2
 Math.LN10
 Math.LOG2E
 Math.LOG10E
```

**算数方法**

下面的例子使用了 Math 对象的 round 方法对一个数进行四舍五入:

```
document.write(Math.round(4.7));
```

下面的例子使用了 Math 对象的 random() 方法来返回一个介于 0 和 1 之间的随机数：

```
document.write(Math.random());
```





