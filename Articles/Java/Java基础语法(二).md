## Java修饰符

Java语言提供了很多修饰符，主要分为以下两类：

* 访问修饰符
* 非访问修饰符

修饰符用来定义类、方法或者变量，通常放在语句的最前面。

```
public class className {
   // ...
}
private boolean myFlag;
static final double weeks = 9.5;
protected static final int BOXWIDTH = 42;
public static void main(String[] arguments) {
   // 方法体
}
```

**访问控制修饰符**：Java中，可以使用访问控制符来保护对类、变量、方法和构造方法的访问。Java支持4种不同的访问权限。

* default：默认的，在同一包内可见，不使用任何修饰符。
* private：私有的，在同一类内可见。
* public：共有的，对所有类可见。
* protected：受保护的，对同一包内的类和所有子类可见。

**默认访问修饰符-不使用任何关键字**

使用默认访问修饰符声明的变量和方法，对同一个包内的类是可见的。接口里的变量都隐式声明为public static final,而接口里的方法默认情况下访问权限为public。如下例所示，变量和方法的声明可以不使用任何修饰符。

```
String version = "1.5.1";
boolean processOrder() {
   return true;
}
```

**私有访问修饰符-private**

私有访问修饰符是最严格的访问级别，所以被声明为private的方法、变量和构造方法只能被所属类访问，并且类和接口不能声明为private。声明为私有访问类型的变量只能通过类中公共的getter方法被外部类访问。Private访问修饰符的使用主要用来隐藏类的实现细节和保护类的数据。

```
public class Logger {
   private String format;
   public String getFormat() {
      return this.format;
   }
   public void setFormat(String format) {
      this.format = format;
   }
}
```

实例中，Logger类中的format变量为私有变量，所以其他类不能直接得到和设置该变量的值。为了使其他类能够操作该变量，定义了两个public方法：getFormat() （返回format的值）和setFormat(String)（设置format的值）。

**公有访问修饰符-public**

被声明为public的类、方法、构造方法和接口能够被任何其他类访问。如果几个相互访问的public类分布在不同的包中，则需要导入相应public类所在的包。由于类的继承性，类所有的公有方法和变量都能被其子类继承。以下函数使用了公有访问控制：

```
public static void main(String[] arguments) {
   // ...
}
```

Java程序的main() 方法必须设置成公有的，否则，Java解释器将不能运行该类。

**受保护的访问修饰符-protected**

被声明为protected的变量、方法和构造器能被同一个包中的任何其他类访问，也能够被不同包中的子类访问。Protected访问修饰符不能修饰类和接口，方法和成员变量能够声明为protected，但是接口的成员变量和成员方法不能声明为protected。

【注意】父类中声明为public的方法在子类中也必须为public。父类中声明为protected的方法在子类中要么声明为protected，要么声明为public。不能声明为private。父类中声明为private的方法，不能够被继承。

**非访问修饰符**

Java也提供了许多非访问修饰符。

* static修饰符，用来创建类方法和类变量。
* final修饰符，用来修饰类、方法和变量，final修饰的类不能够被继承，修饰的方法不能被继承类重新定义，修饰的变量为常量，是不可修改的。
* abstract修饰符，用来创建抽象类和抽象方法。
* synchronized和volatile修饰符，主要用于线程的编程。


**static修饰符**

* 静态变量：static关键字用来声明独立于对象的静态变量，无论一个类实例化多少对象，它的静态变量只有一份拷贝。静态变量也被称为类变量。局部变量不能被声明为static变量。
* 静态方法：static关键字用来声明独立于对象的静态方法。静态方法不能使用类的非静态变量。静态方法从参数列表得到数据，然后计算这些数据。

对类变量和方法的访问可以直接使用classname.variablename和classname.methodname的方式访问。

**final修饰符**

* final变量：final变量能被显式地初始化并且只能初始化一次。被声明为final的对象的引用不能指向不同的对象。但是final对象里的数据可以被改变。也就是说final对象的引用不能改变，但是里面的值可以改变。final修饰符通常和static修饰符一起使用来创建类常量。

```
public class Test{
  final int value = 10;
  // 下面是声明常量的实例
  public static final int BOXWIDTH = 6;
  static final String TITLE = "Manager";

  public void changeValue(){
     value = 12; //将输出一个错误
  }
}
```

* final方法：类中的Final方法可以被子类继承，但是不能被子类修改。声明final方法的主要目的是防止该方法的内容被修改。

```
public class Test{
    public final void changeName(){
       // 方法体
    }
}
```

* final类：final类不能被继承，没有类能够继承final类的任何特性。

```
public final class Test {
   // 类体
}
```

**abstract修饰符**

* 抽象类：抽象类不能用来实例化对象，声明抽象类的唯一目的是为了将来对该类进行扩充。一个类不能同时被abstract和final修饰。如果一个类包含抽象方法，那么该类一定要声明为抽象类，否则将出现编译错误。抽象类可以包含抽象方法和非抽象方法。

```
abstract class Caravan{
   private double price;
   private String model;
   private String year;
   public abstract void goFast(); //抽象方法
   public abstract void changeColor();
}
```

* 抽象方法：抽象方法是一种没有任何实现的方法，该方法的的具体实现由子类提供。抽象方法不能被声明成final和static。任何继承抽象类的子类必须实现父类的所有抽象方法，除非该子类也是抽象类。如果一个类包含若干个抽象方法，那么该类必须声明为抽象类。抽象类可以不包含抽象方法。抽象方法的声明以分号结尾，例如：public abstract sample();

```
public abstract class SuperClass{
    abstract void m(); //抽象方法
}
 
class SubClass extends SuperClass{
     //实现抽象方法
      void m(){
          .........
      }
}
```

**synchronized修饰符**

synchronized关键字声明的方法同一时间只能被一个线程访问。Synchronized修饰符可以应用于四个访问修饰符(public/private/protected/default)。

```
public synchronized void showDetails(){
.......
} 
```

**transient修饰符**

序列化的对象包含被transient修饰的实例变量时，java虚拟机(JVM)跳过该特定的变量。该修饰符包含在定义变量的语句中，用来预处理类和变量的数据类型。

```
public transient int limit = 55;   // will not persist
public int b; // will persist
```

**volatile修饰符**

volatile修饰的成员变量在每次被线程访问时，都强迫从共享内存中重读该成员变量的值。而且，当成员变量发生变化时，强迫线程将变化值回写到共享内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。一个volatile对象引用可能是null。


## 流程控制语句

* 顺序语句
* 判断语句（选择语句）：if、switch

```
//(1)
if (表达式) {

}
//(2)
if (表达式){

} else {

}
//(3)
if () {

} else if () {

} else {
}
```

switch语句：

```
switch (表达式){
   case: 常量值1
      语句体1
      break;
   case: 常量值2
      语句体2
      break;  
    ...
    default:
      语句体n+1;
      break;
}
```

switch语句的注意事项：

```
1. 多个case后面的数值不可以重复
2.switch后面小括号当中只能是下列数据数据类型：
   基本数据类型：byte/short/char/int
   引用数据类型：String字符串、enum枚举
3.switch语句格式可以很灵活，前后顺序可以颠倒，而且break语句还可以省略。匹配哪一个case就从哪一个位置向下执行，直到遇到了break或者整体结束为止。
```

* 循环语句：

### Java 循环语句

Java中有三种主要的循环结构：

* while循环
* do...while循环
* for循环

**while循环**

```
初始化表达式;
while(布尔表达式) {
   //循环内容
   循环体;
   步进表达式;
}
```

只要布尔表达式为true，循环体一直执行下去。

**do...while循环**

do...while循环和while循环相似，不同的是，不论条件成立与否，do...while循环都会至少执行一次。

```
初始化表达式;
do {
   循环体;
   步进表达式;
} while(布尔表达式);//千万不要把结尾的分号丢掉
```

布尔表达式在循环体的后面，所以语句块在检测布尔表达式之前已经执行了。 如果布尔表达式的值为true，则语句块一直执行，直到布尔表达式的值为false。


**for循环**

语法格式：

```
for(初始化表达式; 布尔表达式;变量自增/步进表达式) {
   //代码语句
   循环体;
}
```

最先执行初始化步骤。可以声明一种类型，但可初始化一个或多个循环控制变量，也可以是空语句。然后，检测布尔表达式的值。如果为true，循环体被执行。如果为false，循环终止，开始执行循环体后面的语句。执行一次循环后，更新循环控制变量。再次检测布尔表达式。循环执行上面的过程。

**Java增强for循环**

Java5引入了一种主要用于数组的增强型for循环。Java增强for循环语法格式如下：

```
for(声明语句 : 表达式){

}
```

声明语句：声明新的局部变量，该变量的类型必须和数组元素的类型匹配。其作用域限定在循环语句块，其值与此时数组元素的值相等。

表达式：表达式是要访问的数组名，或者是返回值为数组的方法。

**break关键字**

break主要用在循环语句或者switch语句中，一旦执行，整个循环语句或switch语句立刻结束。

**continue关键字**

continue适用于任何循环控制结构中。作用是让程序立刻结束本次循环并执行下一次循环。

## IDE(集成开发环境) & IntelliJ IDEA

免费的IDE有Eclipse，收费的IDE是IntelliJ IDEA（使用最多）。

## IDEA的项目结构

Project -> Module(模块) -> Package（包）

需要注意的是：所有代码都写在src

## 方法

### 方法定义的完整格式：

```
修饰符 返回值类型 方法名称 (参数类型 参数名称1, 参数名称2, ...) {
   方法体
   return 返回值;
}
```

方法三要素：返回值类型、方法名称和参数列表。

### 方法的三种调用格式

* (1)单独调用：

```
方法名称(参数);
```
* (2)打印调用：

```
System.out.println();
```
* (3)赋值调用：

```
数据类型 变量名称 = 方法名称(参数);
```


### 方法的注意事项

* 方法应该定义在类当中，但是不能在方法当中再定义方法，不能嵌套
* 方法定义的前后顺序无所谓
* 方法定义之后不会执行，一定要调用
* 如果方法有返回值，那么必须写上“return 返回值”，不能没有；
* return后面的返回值数据，必须和方法的返回值类型一致。
* 对于一个void没有返回值的方法，不能写return后面的返回值，只能写return自己；

### 方法重载（Overload）

方法重载：多个方法的名称一样，但是参数列表不一样，注意与方法的返回值类型无关。其中参数列表不一样是指参数的个数不同或参数的类型不同，或参数的多类型的顺序不同。

好处：只需要记住一个方法名称，就可以实现多个类似的功能。

```
public static int sum(int a, int b) {
   return a + b;
}
public static int sum(int a, int b, int c) {
   return a + b + c;
}
```

## 数组

**数组的概念**：数组是一种容器，可以同时存放多个相同类型的值。

**数组的特点**：

* 数组是一种引用数据类型；
* 数组当中的多个数据的类型必须相同；
* 数组的长度在程序运行期间不可改变。

**数组的初始化**

数组的初始化：在内存当中创建一个数组并向其中赋予一些默认值。

数组初始化的两种方式：

* 动态初始化（指定长度），如果是整数类型，那么默认为0，如果是浮点类型，那么默认为0.0；如果是字符类型，那么默认为'\u0000'；如果是布尔类型，那么默认为false；如果是引用类型，那么默认为null。

```
//(1)动态初始化格式：
数据类型[] 数组名称 = new 数据类型(数组长度);
//(2)静态初始化的省略格式：
数据类型[] 数组名称 = {元素1, 元素2, ...};
```

* 静态初始化（指定内容）：在创建数组的时候，直接指定具体的内容。

```
//(1)静态初始化的基本(标准)格式：
数据类型[] 数组名称 = new 数据类型[] {元素1, 元素2, ...};
int [] arr1 = new int {5, 15, 20};
String[] arrS = new String[] {"hello", "world"};

//(2)静态初始化的省略格式：
int [] arr1 = {5, 15, 20};
```

注意事项：

* 1.静态初始化没有直接指定长度，但是仍然会自动推算得到长度。
* 2.静态初始化标准格式可以拆分成为两个步骤；
* 3.动态初始化也可以拆分为两个步骤
* 4. 静态初始化的省略格式，不能拆分成为两个步骤了。


## Java 内存划分

![image.png](https://upload-images.jianshu.io/upload_images/4164292-8a2304de098f64c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 数组内存分布图

![image.png](https://upload-images.jianshu.io/upload_images/4164292-ee83e36f0c9d111a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

















