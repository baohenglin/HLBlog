

Category是Objective-C 2.0之后增加的语言特性，Category的主要作用是为已经存在的类添加方法。Category是对装饰模式的典型实践（装饰模式(Decorator)是指在不修改原有类的前提下，动态地给这个类添加一些方法）。

## Category的底层结构

![Category的底层结构示意图](https://upload-images.jianshu.io/upload_images/4164292-5889a06649ab7c83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'Category的底层结构示意图')


## category的加载处理过程

（1）通过Runtime加载某个类的所有Category数据;

（2）把所有Category的方法、属性、协议数据，合并到一个大数组中，后面参与编译的Category数据，会在数组的前面；

（3）将合并后的分类数据（方法、属性、协议），插入到类原来数据的前面。

【注意】category并没有完全的替换掉原有类的同名方法，category的方法被放置在新方法列表的前面，而原来类的方法被放到后面，在runtime中，遍历方法列表查找时，找到了category的方法后，就会停止遍历，这就是我们平时所说的“覆盖”方法。

&emsp;&emsp;某个类如果存在两个category，而且这两个Category中存在方法名相同的方法，那么根据buildPhases->Compile Sources里面的从上至下的编译顺序，会调用后参与编译的那个分类的方法。

## Category的局限性

* Category只能给某个现有的类添加方法，不能添加成员变量；
* Category中也可以添加属性，只不过@property只会生成setter和getter的声明，不会生成setter方法和getter方法的实现，也不会自动合成带下划线的成员变量；
* 如果category中的方法和类中原有方法同名，运行时会优先调用category中的方法。也就是，category中的方法会覆盖掉类中原有的方法。所以开发中尽量保证不要让分类中的方法和原有类中的方法名相同。避免出现这种情况的解决方案是给分类的方法名统一添加前缀。比如category_。

## +load方法

### +load方法的特点
* +load方法会在runtime加载类、分类时调用其对应的+load方法。
* 每个类、分类的+load方法，在程序运行过程中只调用一次。

### +load方法的调用顺序

根据Apple的objc-runtime-new.mm的源码分析可知+load方法的调用顺序如下：

1.先调用类的+load方法

* 按照编译的先后顺序调用（先编译，先调用）
* 调用子类的+load方法之前会先调用父类的+load

2.再调用分类的+load方法

* 按照编译先后顺序调用（先编译，先调用）


## +initialize方法

### +initialize方法的特点

* +initialize方法会在类第一次接收到消息的时候调用


### +initialize方法的调用顺序

调用顺序：先调用父类的+initialize，再调用子类的+initialize。也就是先初始化父类，再初始化子类，且每个类只会初始化1次。

+initialize和+load的最大区别是，+initialize是通过objc_msgSend进行调用的，而+load方法是通过函数指针直接调用+load方法。正因为+initialize是通过objc_msgSend进行调用的，所以+initialize有以下特点：

* 如果子类没有实现+initialize，会调用父类的+initialize（所以父类的+initialize可能会被调用多次）；
* 如果分类实现了+initialize，就覆盖类本身的+initialize调用。

## 有关Category常见的面试题

1.Category的使用场合是什么?

* 可以为现有类添加新的方法；
* 模拟多继承
* 将一个类中的代码分散出来管理（将类的实现分布到几个不同文件中）
* 把framework的私有方法公开


2.Category的实现原理是什么?

&emsp;&emsp;Category编译之后的底层结构是struct_category_t，里面存储着分类的对象方法、类方法、属性、协议信息。在程序运行的时候，runtime会将Category的数据，合并到类信息中（类对象、元类对象中）

3.Category和Class Extension的区别是什么?

&emsp;&emsp;Class Extension是在编译期决定，Category由运行时决定。也就是说Class extension在编译的时候，它的数据就已经包含在类信息中；而Category（分类）是在运行时，才会将分类数据合并到类信息中（类对象、元类对象中）。

4.Category能否添加成员变量?如果可以,如何给Category添加成员变量?

默认情况下，由于分类底层结构的限制，不能直接给Category添加成员变量,但是可以间接实现。

&emsp;&emsp;通过“关联对象”的方式来给Category添加成员变量。“<objc/runtime.h>”中提供的关联对象的API有以下3个：

* (1)添加关联对象

&emsp;&emsp; void objc_setAssociatedObject(id object, const void * key,id value, objc_AssociationPolicy policy)

* (2)获得关联对象

&emsp;&emsp;id objc_getAssociatedObject(id object, const void * key)

* (3)移除所有的关联对象

&emsp;&emsp; void objc_removeAssociatedObjects(id object)

5.Category中有load方法吗?load方法是什么时候调用的?load方法能继承吗?

&emsp;&emsp;有+load方法。load方法在runtime（运行时）加载类、分类的时候调用。+load方法能继承。
【注意】+load方法一般都由系统自动调用，不手动调用。

6.load方法和initialize方法的区别是什么?它们在category中的调用顺序是怎样的?以及出现继承时它们之间的调用过程是怎样的?

区别：

* （1）调用方式不同。load是根据函数地址直接调用，而initialize是通过objc_msgSend调用

* （2）调用时刻不同。load是runtime加载类、分类的时候调用（只会调用1次），而initialize是类第一次接收到消息的时候调用，每一个类只会initialize一次（父类的initialize方法可能会被调用多次）。

load、initialize的调用顺序：

1. load

1> 先调用类的+load方法

a)按照编译的先后顺序调用（先编译，先调用）

b)调用子类的+load方法之前会先调用父类的+load

2> 再调用分类的+load方法

a)按照编译先后顺序调用（先编译，先调用）


2. initialize

1> 先初始化父类;

2> 再初始化子类(可能最终调用的是父类的initialize方法)。

## OC的类扩展(Class Extension)

类扩展(Class Extension),也称为匿名分类。可以为原来的类 扩展一些属性、成员变量以及方法。

<br>
<br>
<br>

#### 参考链接:

<https://www.jianshu.com/p/f673206c5abc>





