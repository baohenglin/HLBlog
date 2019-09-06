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

## Java循环结构

Java中有三种主要的循环结构：

* while循环
* do...while循环
* for循环

**while循环**

```
while(布尔表达式) {
   //循环内容
}
```

只要布尔表达式为true，循环体一直执行下去。

**do...while循环**

do...while循环和while循环相似，不同的是，不论条件成立与否，do...while循环都会至少执行一次。

```
do {
   //循环内容
} while(布尔表达式);
```

布尔表达式在循环体的后面，所以语句块在检测布尔表达式之前已经执行了。 如果布尔表达式的值为true，则语句块一直执行，直到布尔表达式的值为false。


**for循环**

语法格式：

```
for(初始化; 布尔表达式;变量自增) {
   //代码语句
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














