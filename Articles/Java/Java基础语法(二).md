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





















