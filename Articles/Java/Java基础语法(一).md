

## 二进制

如何将十进制数字转换成二进制的数字？使用**辗转相除法**

![image.png](https://upload-images.jianshu.io/upload_images/4164292-26b27e00c2469a95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将二进制数字转化为十进制数字：

![image.png](https://upload-images.jianshu.io/upload_images/4164292-429c3de1e316966c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 位（bit）& 字节（Byte）

**位**（bit）：一个数字0或者一个数字1代表一位。

**字节**（Byte）：每8位（二进制位）表示一个字节。字节是计算机中**数据存储的最小存储单元**。也就是说计算机存储任何数据都是以字节的形式存储。

```
1 Byte = 8 bit
1 KB = 1024 Byte
1 MB = 1024 KB
1 GB = 1024 MB
1 TB = 1024 GB
1 PB = 1024 TB
1 EB = 1024 PB
1 ZB = 1024 EB
```

## 命令提示符 cmd

启动终端命令： Win键 + R， 然后再输入“cmd”，回车即可。


```
(1)切换盘符命令：盘符名称 + ":"
(2)进入文件夹命令：cd 文件名
(3)进入多级文件夹：cd 文件夹1\文件夹2\文件夹3
(4)返回上一层文件夹命令：cd..
(5)返回到根路径命令：cd \
(6)清空终端输入的所有命令：cls
(7)查看文件夹详情：dir
(8)退出命令：exit
```

## Java简介

Java是一门面向对象编程语言，不仅。吸收了C++语言的各种优点，还摒弃了C++里难以理解的多继承、指针等概念，因此Java语言具有功能强大和简单易用两个特征。Java语言作为静态面向对象编程语言的代表，极好地实现了面向对象理论，允许程序员以优雅的思维方式进行复杂的编程。

Java具有简单性、面向对象、分布式、健壮性、安全性、平台独立与可移植性、多线程、动态性等特点。Java可以编写桌面应用程序、Web应用程序、分布式系统和嵌入式系统应用程序等。

Java是由Sun Microsystems公司于1995年5月推出的Java面向对象程序设计语言和Java平台的总称。由James Gosling和同事们共同研发，并在1995年正式推出。

Java分为三个体系：

* JavaSE(J2SE)(Java2 Platform Standard Edition，java平台标准版)
* JavaEE(J2EE)(Java 2 Platform,Enterprise Edition，java平台企业版)
* JavaME(J2ME)(Java 2 Platform Micro Edition，java平台微型版)

2005年6月，JavaOne大会召开，SUN公司公开Java SE 6。此时，Java的各种版本已经更名以取消其中的数字"2"：J2EE更名为Java EE, J2SE更名为Java SE，J2ME更名为Java ME。

## Java 主要特性
* **跨平台**：任何软件的运行都必须要运行在操作系统之上，而我们用java编写的程序能够运行在任何的操作系统上，这个特性称为Java语言的跨平台性。跨平台性是由JVM实现的，我们编写的程序运行在JVM上，而JVM运行在操作系统上。JVM（Java Virtual Machine），即Java虚拟机。JVM是Java程序的运行环境，我们编写的Java程序都运行在JVM上。

![image.png](https://upload-images.jianshu.io/upload_images/4164292-2a2c918c9aa3611c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **Java语言是简单的**：Java语言的语法与C语言和C++语言很接近，使得大多数程序员很容易学习和使用。另一方面，Java丢弃了C++中很少使用的、很难理解的、令人迷惑的那些特性，如操作符重载、多继承、自动的强制类型转换。特别地，Java语言不使用指针，而是引用。并提供了自动的废料收集，使得程序员不必为内存管理而担忧。
* **Java语言是面向对象的**：Java语言提供类、接口和继承等原语，为了简单起见，只支持类之间的单继承，但支持接口之间的多继承，并支持类与接口之间的实现机制（关键字为implements）。Java语言全面支持动态绑定，而C++语言只对虚函数使用动态绑定。总之，Java语言是一个纯的面向对象程序设计语言。
* **Java语言是分布式的**：Java语言支持Internet应用的开发，在基本的Java应用编程接口中有一个网络应用编程接口（java net），它提供了用于网络应用编程的类库，包括URL、URLConnection、Socket、ServerSocket等。Java的RMI（远程方法激活）机制也是开发分布式应用的重要手段。
* **Java语言是健壮的**：Java的强类型机制、异常处理、垃圾的自动收集等是Java程序健壮性的重要保证。对指针的丢弃是Java的明智选择。Java的安全检查机制使得Java更具健壮性。
* **Java语言是安全的**：Java通常被用在网络环境中，为此，Java提供了一个安全机制以防恶意代码的攻击。除了Java语言具有的许多安全特性以外，Java对通过网络下载的类具有一个安全防范机制（类ClassLoader），如分配不同的名字空间以防替代本地的同名类、字节代码检查，并提供安全管理机制（类SecurityManager）让Java应用设置安全哨兵。
* **Java语言是体系结构独立的**：Java程序（后缀为java的文件）在Java平台上被编译为体系结构中立的字节码格式（后缀为class的文件），然后可以在实现这个Java平台的任何系统中运行。这种途径适合于异构的网络环境和软件的分发。
* **Java语言是可移植的**：这种可移植性来源于体系结构中立性，另外，Java还严格规定了各个基本数据类型的长度。Java系统本身也具有很强的可移植性，Java编译器是用Java实现的，Java的运行环境是用ANSI C实现的。
* **Java语言是解释型的**：如前所述，Java程序在Java平台上被编译为字节码格式，然后可以在实现这个Java平台的任何系统中运行。在运行时，Java平台中的Java解释器对这些字节码进行解释执行，执行过程中需要的类在联接阶段被载入到运行环境中。
* **Java语言是高性能的**：与那些解释型的高级脚本语言相比，Java的确是高性能的。事实上，Java的运行速度随着JIT(Just-In-Time）编译器技术的发展越来越接近于C++。
* **Java语言是多线程的**：在Java语言中，线程是一种特殊的对象，它必须由Thread类或其子（孙）类来创建。通常有两种方法来创建线程：其一，使用型构为Thread(Runnable)的构造子将一个实现了Runnable接口的对象包装成一个线程，其二，从Thread类派生出子类并重写run方法，使用该子类创建的对象即为线程。值得注意的是Thread类已经实现了Runnable接口，因此，任何一个线程均有它的run方法，而run方法中包含了线程所要运行的代码。线程的活动由一组方法来控制。Java语言支持多个线程的同时执行，并提供多线程之间的同步机制（关键字为synchronized）。
* **Java语言是动态的**：Java语言的设计目标之一是适应于动态变化的环境。Java程序需要的类能够动态地被载入到运行环境，也可以通过网络来载入所需要的类。这也有利于软件的升级。另外，Java中的类有一个运行时刻的表示，能进行运行时刻的类型检查。

## JRE、JDK和JVM

**JDK**（Java Development Kit）：是 Java 程序开发工具包，包含 JRE 和开发人员使用的工具。如果我们想要开发一个全新的 Java 程序，那么必须安装 JDK。

**JRE**（Java Runtime Environment）：是 **Java 程序的运行时环境，包含 JVM和运行时所需要的 核心类库**。如果我们想要运行一个已开发完成的 Java 程序，那么只需要安装 JRE 即可。

**JVM**（Java Virtual Machine），即Java虚拟机。JVM是Java程序的运行环境，我们编写的Java程序都运行在JVM上。

![image.png](https://upload-images.jianshu.io/upload_images/4164292-f2af12bdc9131c38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Java 程序开发步骤

![image.png](https://upload-images.jianshu.io/upload_images/4164292-11be2e08a4b4d7ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Java 基础语法

一个Java程序可以认为是一系列对象的集合，而这些对象通过调用彼此的方法来协同工作。下面简要介绍下类、对象、方法和实例变量的概念。

**对象**：对象是类的一个实例，有状态和行为。例如，一条狗是一个对象，它的状态有：颜色、名字、品种；行为有：摇尾巴、叫、吃等。

**类**：类是一个模板，它描述一类对象的行为和状态。

**方法**：方法就是行为，一个类可以有很多方法。逻辑运算、数据修改以及所有动作都是在方法中完成的。

**实例变量**：每个对象都有独特的实例变量，对象的状态由这些实例变量的值决定。

编写Java程序时，应注意以下几点：

* 大小写敏感：Java是大小写敏感的，这就意味着标识符Hello与hello是不同的。
* 类名：对于所有的类来说，类名的首字母应该大写。如果类名由若干单词组成，那么每个单词的首字母应该大写，例如 MyFirstJavaClass 。
* 方法名：所有的方法名都应该以小写字母开头。如果方法名含有若干单词，则后面的每个单词首字母大写。
* 源文件名：源文件名必须和类名相同。当保存文件的时候，你应该使用类名作为文件名保存（切记Java是大小写敏感的），文件名的后缀为.java。（如果文件名和类名不相同则会导致编译错误）。
* 主方法入口：所有的Java 程序由public static void main(String args[])方法开始执行。

**Java标识符**

类名、变量名以及方法名都被称为标识符。关于Java标识符，有以下几点需要注意：

**标识符命名规则**（硬性要求）：

* 标识符可以包含字母（A-Z或者a-z）、0-9的数字、美元符（$）、或者下划线（_）；
* 标识符不能以数字开头；
* 关键字不能用作标识符。
* 标识符是大小写敏感的。

**命名规则**（软性要求）：

* 类名规范：首字母大写，后面每个单词的首字母大写（大驼峰式）；
* 变量名规范：首字母小写，后面每个单词的首字母大写（小驼峰式）；
* 方法名规范：同变量名。

## Java 对象和类

看看周围真实的世界，会发现身边有很多对象，车，狗，人等等。所有这些对象都有自己的状态和行为。拿一条狗来举例，它的状态有：名字、品种、颜色，行为有：叫、摇尾巴和跑。对比现实对象和软件对象，它们之间十分相似。软件对象也有状态和行为。软件对象的状态就是属性，行为通过方法体现。

一个类可以包含以下类型变量：

* **局部变量**：在方法、构造方法或者语句块中定义的变量被称为局部变量。变量声明和初始化都是在方法中，方法结束后，变量就会自动销毁。
* **成员变量**：成员变量是定义在类中，方法体之外的变量。这种变量在创建对象的时候实例化。成员变量可以被类中方法、构造方法和特定类的语句块访问。
* **类变量**：类变量也声明在类中，方法体之外，但必须声明为static类型。

```
public class HLDog {
  //成员变量
	String breed;
	int age;
	String color;
  //方法
	void barking() {
		
	}
	void hungry() {
		
	}
	void sleeping() {
		
	}
}
```

**构造方法**

每个类都有构造方法。如果没有显式地为类定义构造方法，Java编译器将会为该类提供一个默认构造方法。在创建一个对象的时候，至少要调用一个构造方法。构造方法的名称必须与类同名，一个类可以有多个构造方法。

```
public class HLDog {
	//构造方法
	public HLDog() {
		
	}
	public HLDog(String name) {
		//这个构造器仅有一个参数：name
	}
}
```

## Java基本数据类型

Java的两大数据类型：内置的基本数据类型和引用数据类型（字符串、数组、类、接口、Lambda）。

![image.png](https://upload-images.jianshu.io/upload_images/4164292-ba7eccb6f26e1d4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**内置数据类型**：Java语言提供了八种基本类型。6种数字类型（四个整数型，两个浮点型），一种字符类型，还有一种布尔型。

* **(1)byte**：byte数据类型是8位、有符号的，以二进制补码表示的整数。最小值是-128(-2<sup>^</sup>7)，最大值是127(2<sup>^7</sup>-1)，默认值是0。byte类型用在大型数组中节约空间，主要代替整数，因为byte变量占用的空间只有int类型的四分之一。

```
byte a = 100
byte b = -50
```
* **(2)short**：short数据类型是16位，有符号的以二进制补码表示的整数。最小值时-32768(-2<sup>^</sup>15)，最大值是32767(2<sup>^</sup>15-1)，short数据类型也可以像byte那样节省空间。一个short变量是int型变量所占空间的二分之一。默认值是0.

```
short s = 1000
short r = -2000
```

* **(3)int**：int数据类型是32位，有符号的以二进制补码表示的整数。最小值是-2147483648(-2<sup>^</sup>31)，最大值是2147483647(2<sup>^</sup>31-1)，一般的整型变量默认为int类型。默认值是0。

```
int a = 100000
int b = -2000
```

* **(4)long**：long数据类型是64位、有符号的以二进制补码表示的整数。最小值是-9,223,372,036,854,775,808（-2<sup>^</sup>63），最大值是9,223,372,036,854,775,807（2<sup>^</sup>63 -1）。long类型主要使用在需要比较大整数的系统上。默认值是0L。

```
long a = 100000L
long b = -200000L
```

* **(5)float**：float数据类型是单精度、32位、符合IEEE 754标准的浮点数。float在储存大型浮点数组的时候可节省内存空间。默认值是0.0f。浮点数不能用来表示精确的值。

```
float f1 = 234.5f
```

* **(6)double**：double数据类型是双精度、64位、符合IEEE 754标准的浮点数。浮点数的默认类型为double类型。默认值为0.0d。double类型同样不能表示精确的值。

* **(7)boolean**：boolean数据类型表示一位的信息。只有两个取值：true和false。默认值为false。
* **(8)char**：char类型是一个单一的16位Unicode字符。最小值是’\u0000’（即为0）。最大值是’\uffff’（即为65,535）。char数据类型可以存储任何字符。

```
char letter = 'A'
```

【注意】实际上，JAVA中还存在另外一种基本类型void，它也有对应的包装类 java.lang.Void，不过我们无法直接对它们进行操作。

**基本数据类型注意事项**：

* （1）字符串不是基本数据类型，而是引用类型；
* （2）浮点型可能只是一个近似值，并非精确的值；
* （3）数据范围与字节数不一定相关。例如float数据范围比long更加广泛，但是float是4个字节，long是8个字节；
* （4）浮点数当中默认是double，如果一定要使用float类型，需要加上一个后缀F；如果是整数，默认是int类型，如果一定要使用long类型，需要加上后缀L。
* （5）对于byte/short/char三种类型来说，如果右侧赋值的数值没有超过范围，那么javac编译器将会自动隐含地为我们补上一个(byte)(short)(char)。如果没有超过左侧的范围，编译器补上强转；如果右侧超过了左侧范围，那么直接编译报错。

```
byte num1 = 30;//右侧没有超过左侧的范围，编译器会自动补上(byte)
byte num1 = 128;//右侧超过了左侧的范围，编译器报错。
char zifu = 65;//右侧没有超过左侧的范围，编译器会自动补上(char)
```

在变量进行赋值时，如果右侧的表达式当中全是常量，没有任何变量，那么编译器javac将会直接将若干个常量表达式计算得到结果。比如 short result = 5 + 8; 编译之后，得到的 class 字节码文件当中相当于直接就是 short result = 13;右侧的常量结果数值，没有超过左侧范围，所以正确。这称为“编译器的常量优化”。但是请注意，一旦表达式当中有变量参与，那么就不能进行这种优化了。

```
short a = 5;
short b = 8;
// short + short -> int + int -> int
short result = a + b;
//编译器报错，左侧应该是int类型
```

**引用类型**：引用类型变量由类的构造函数创建，可以使用它们访问所引用的对象。这些变量在声明时被指定为一个特定的类型，比如Employee、Pubby等。变量一旦声明后，类型就不能被改变了。对象、数组都是引用数据类型。所有引用类型的默认值都是null。

```
Animal animal = new Animal("giraffe")
```

字面量可以赋值给任何内置类型的变量。例如：

```
byte a = 68;
char b = 'B'
```

byte、int、long和short都可以用十进制、十六进制以及八进制的方式来表示。当使用常量的时候，前缀0表示是八进制，而前缀0x表示是十六进制。

```
int decimal = 100;
int octal = 0144;//八进制
int hexa = 0x64;//十六进制
```

## Java常量

常量是指在程序运行期间固定不变的量。它们不需要计算，直接代表相应的值。常量包括：字符串常量、整数常量、浮点数常量、字符常量。

* 字符串常量：凡是使用双引号引起来的部分，都是字符串常量。例如："abc"、"Hello"、"123"
* 整数常量：直接写上的数字，没有小数点的。例如：100、0、-200
* 浮点数常量：直接写上的数字，而且有小数点。例如：2.5、-3.14、0.0
* 字符常量：凡是用单引号引起来的单个字符，都是字符常量。需要注意的是**两个单引号之间必须有且只有一个字符**。例如：'A'、'b'、'9'、'中'
* 布尔常量：只有两种取值：true 和 false
* 空常量：null，代表没有任何数据。

在Java中用final来声明常量，声明方式和变量类似：

```
final double PI = 3.1415926;
```

虽然常量名也可以用小写，但为了便于识别，通常使用**大写字母表示常量**。

## Java变量类型

### Java变量 注意事项：

* （1）如果创建多个变量，那么变量名称不能重复；
* （2）对于float和long类型，字母后缀F和L不要丢掉；
* （3）如果使用byte和short类型的变量，那么右边的数值不能超过左边类型的范围；
* （4）没有进行初始化（赋值）的变量，不能直接使用。也就是使用前必须先初始化。
* （5）变量使用不能超出其作用域（从定义变量那一行开始，一直到直接所属的大括号结束为止）。

在Java语言中，所有的变量在使用前必须声明。声明变量的基本格式如下：

```
type identifier [ = value][, identifier [= value] ...] ;
```

格式说明：type为Java数据类型。identifier是变量名。可以使用逗号隔开来声明多个同类型变量。以下列出了一些变量的声明实例。注意有些包含了初始化过程。

```
int a, b, c;         // 声明三个int型整数：a、b、c。
int d = 3, e, f = 5; // 声明三个整数并赋予初值。
byte z = 22;         // 声明并初始化z。
double pi = 3.14159; // 声明了pi。
char x = 'x';        // 变量x的值是字符'x'。
```

Java语言支持的变量类型有：**局部变量**、**实例变量**、**类变量**。

* **Java局部变量**：局部变量声明在方法、构造方法或者语句块中。局部变量在方法、构造方法、或者语句块被执行的时候创建，当它们执行完成后，变量将会被销毁。访问修饰符不能用于局部变量。局部变量只在声明它的方法、构造方法或者语句块中可见。局部变量是在栈上分配的。局部变量没有默认值，所以局部变量被声明后，必须经过初始化，才可以使用。
* **Java实例变量**：实例变量声明在一个类中，但在方法、构造方法和语句块之外。当一个对象被实例化之后，每个实例变量的值就跟着确定。实例变量在对象创建的时候创建，在对象被销毁的时候销毁。实例变量的值应该至少被一个方法、构造方法或者语句块引用，使得外部能够通过这些方式获取实例变量信息。实例变量可以声明在使用前或者使用后。访问修饰符可以修饰实例变量。实例变量对于类中的方法、构造方法或者语句块是可见的。一般情况下应该把实例变量设为私有。通过使用访问修饰符可以使实例变量对子类可见。实例变量具有默认值。数值型变量的默认值是0，布尔型变量的默认值是false，引用类型变量的默认值是null。变量的值可以在声明时指定，也可以在构造方法中指定。实例变量可以直接通过变量名访问。但在静态方法以及其他类中，就应该使用完全限定名：ObejectReference.VariableName。

```
import java.io.*;
public class Employee{
   // 这个成员变量对子类可见
   public String name;
   // 私有变量，仅在该类可见
   private double salary;
   //在构造器中对name赋值
   public Employee (String empName){
      name = empName;
   }
   //设定salary的值
   public void setSalary(double empSal){
      salary = empSal;
   }  
   // 打印信息
   public void printEmp(){
      System.out.println("name  : " + name );
      System.out.println("salary :" + salary);
   }

   public static void main(String args[]){
      Employee empOne = new Employee("Ransika");
      empOne.setSalary(1000);
      empOne.printEmp();
   }
}
```

* **Java类变量(静态变量)**

类变量也称为静态变量，在类中以**static关键字声明**，但必须在方法、构造方法和语句块之外。无论一个类创建了多少个对象，类只拥有类变量的一份拷贝。静态变量除了被声明为常量外很少使用。常量是指声明为public/private，final和static类型的变量。常量初始化后不可改变。静态变量储存在静态存储区。经常被声明为常量，很少单独使用static声明变量。静态变量在程序开始时创建，在程序结束时销毁。与实例变量具有相似的可见性。但为了对类的使用者可见，大多数静态变量声明为public类型。默认值和实例变量相似。数值型变量默认值是0，布尔型默认值是false，引用类型默认值是null。变量的值可以在声明的时候指定，也可以在构造方法中指定。此外，静态变量还可以在静态语句块中初始化。静态变量可以通过：ClassName.VariableName的方式访问。**类变量被声明为public static final类型时，类变量名称必须使用大写字母。如果静态变量不是public和final类型，其命名方式与实例变量以及局部变量的命名方式一致**。

```
import java.io.*;
public class Employee{
   //salary是静态的私有变量
   private static double salary;
   // DEPARTMENT是一个常量
   public static final String DEPARTMENT = "Development ";
   public static void main(String args[]){
      salary = 1000;
      System.out.println(DEPARTMENT+"average salary:"+salary);
   }
}
```

如果其他类想要访问该变量，可以这样访问：Employee.DEPARTMENT。


## Java类型转换

当数据类型不一致时，将会发生数据类型转换。包括：

* 自动类型转换（隐式）：代码不需要进行特殊处理，自动完成。自动类型的转换规则是**数据范围从小到大**(int -> long、float -> double、long -> float)。
* 强制类型转换（显式）：代码需要进行特殊的格式处理，不能自动完成。

```
//int num = 100L; 需要强制类型转换
int num = (int)100L;
```

**注意事项**：

* （1）不推荐使用强制类型转换，因为可能发生精度损失或者数据溢出。

```
int num2 = (int)6000000000;//数据溢出
int num3 = (int)3.9;//精度损失。结果num=3,这并不是四舍五入，所有的小数都会被省去。
```

* （2）byte/short/char这三种类型都可以发生数学运算（例如加法“+”）

```
//ASCII码表 A -> 65  a -> 97  字符0 -> 48
char char1 = 'A';//字符A在ASCII码表中对应数字65。
System.out.println(char1 + 1);//结果是 66
```

* （3）byte/short/char这三种类型都可以发生数学运算，都会被首先提升成为int类型，然后再计算。

```
byte num4 = 40;
byte num5 = 60;
// byte + byte ---> int + int --->int
int result1 = num4 + num5;
System.out.println(result1);//结果是 100

byte num6 = 70;
// byte + short ---> int + int ---> int 
short result2 = (short)(num4 + num6); //110
```

## ASCII编码表 & Unicode码表

* ASCII码表（American Standard Code for Information Interchange）美国信息交换标准代码
* Unicode码表，万国表，也就是数字和符号的对照关系，开头0-127部分和ASCII完全一样，但是从128开始包含更多字符。

```
'0' -> 48
'A' -> 65
'a' -> 97
```

## Java 相关运算符

### 算数运算符

### 赋值运算符

### 比较运算符

### 逻辑运算符

### 三元运算符

## 方法

### 方法的定义

```
public static void 方法名称() {
  方法体
}
```

### 方法的调用

```
方法名();
```

## JShell（轻量级的脚本工具）

启动命令：jsheel

退出命令：/exit

