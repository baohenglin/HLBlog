### 《iOS之Objective-C对象的本质》
&emsp;&emsp;我们平时编写的Objective-C代码，其底层实现都是C/C++代码，也就是说编译时编译器会先将Objective-C代码转化为C/C++代码，然后再将C/C++代码转化为更为低级的汇编语言，最后再将汇编语言转化为机器语言并运行到终端设备上。

&emsp;&emsp;Objective-C的面向对象都是基于C/C++的数据结构实现的。那么是基于C/C++的什么数据结构实现的呢？答案是结构体(Struct)！

#### 1.1 将OC代码转化为底层的C/C++

&emsp;&emsp;为了研究Objective-C对象的底层实现，我们需要将Objective-C代码转为底层的C/C++代码，那么具体如何转化呢？

&emsp;&emsp;在Xcode中，选择macOS->Command Line Tool，新建一个工程。此时项目中会自动生成一个main.m的文件。main.m中代码如下：

```
int main(int argc, const char * argv[]) {
    @autoreleasepool {
       NSObject *objc = [[NSObject alloc]init];
    }
    return 0;
}
```

&emsp;&emsp;接下来，利用go2shell插件在终端中快速项目路径。然后在终端中执行clang工具的命令行“clang -rewrite-objc main.m -o main.cpp”，就会自动生成main.cpp文件（.cpp是C++文件的后缀）。

```
clang -rewrite-objc main.m -o main.cpp
```

&emsp;&emsp;执行以上命令生成的main.cpp代码将近有10万行。这是因为没有指定运行平台（mac、iOS、windows）以及所支持的架构（模拟器(i386)、32bit(armv7)、64bit(arm64)）。所以建议使用如下命令：

```
xcrun  -sdk iphoneos  clang  -arch  arm64  -rewrite-objc  main.m  -o  main-arm64.cpp

```
【注意】如果报错“xcrun: error: SDK "iphoneos" cannot be located”，指定一下sdk iphoneos的版本（这是因为安装了多个版本的Xcode）。命令如下：

```
xcrun  -sdk iphoneos12.1  clang  -arch  arm64  -rewrite-objc  main.m  -o  main-arm64.cpp
```

&emsp;&emsp;这样，.cpp文件会减小很多，由原来的将近10万行变为3万行左右。

#### 1.2 Objective-C对象分为哪几类？

&emsp;&emsp;Objective-C对象，简称OC对象，主要分为3类。分别是：instance对象（实例对象）、class对象（类对象）和meta-class对象（元类对象）。

#### 1.3 OC对象的底层实现探究

* instance 对象（实例对象）

&emsp;&emsp;instance对象（实例对象）是指通过类调用alloc方法创建出来的对象。每调用一次alloc就会创建出一个新的instance对象。不同的对象，在内存中分别占据着两块不同的存储空间。

&emsp;&emsp;instance对象在内存中存储的信息是“成员变量”（isa指针和其他的成员变量），而不包括方法。


NSObject在Cocoa中NSObject.h的定义如下：

```
@interface NSObject<NSObject> {
	Class isa  OBJC_ISA_AVAILABILITY;
}

typedef struct objc_class *Class
```

由上面生成的main.cpp文件可知，NSObject在C/C++底层的实现如下：

```
//NSObject Implementation
struct NSObject_IMPL {
	Class isa;
}
```

* class对象（类对象）

&emsp;&emsp;那么如何获取类对象呢？具体代码如下：

```
//创建实例对象（instance对象）
NSObject *object1 = [[NSObject alloc]init];
NSObject *object2 = [[NSObject alloc]init];
//获取类对象 方法一
Class objectClass1 = [object1 class];
Class objectClass2 = [object2 class];
//获取类对象 方法二
//class方法不管调用多少次，返回的始终是类对象。
Class objectClass3 = [NSObject class];
//获取类对象 方法三
Class objectClass4 = object_getClass(object1);//Runtime API
Class objectClass5 = object_getClass(object2);//Runtime API

```

&emsp;&emsp;objectClass1~objectClass5都是NSObject的class对象(类对象)而且它们是同一个对象。每个类在内存中有且只有一个class对象。

&emsp;&emsp;class对象在内存中存储的信息主要包括：

&emsp;&emsp;✅（1）isa指针

&emsp;&emsp;✅（2）superclass指针

&emsp;&emsp;✅（3）类的属性信息（@property）

&emsp;&emsp;✅（4）类的对象方法信息（instance method，也就是以“-”开头的方法，而不是类方法（以“+”开头的方法称为类方法））

&emsp;&emsp;✅（5）类的协议信息（protocol）

&emsp;&emsp;✅（6）类的成员变量描述信息（ivar），此处的成员变量信息指的是成员变量的类型、成员变量的名称等，而不是指“实例对象的内存中存储的成员变量的具体值”。

&emsp;&emsp;✅ （7）...

class对象的内存存储信息示意图，具体如下：

![class对象的内存存储信息示意图](https://upload-images.jianshu.io/upload_images/4164292-e6e53caa685bee99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'class对象的内存存储信息示意图')

* meta-class对象（元类对象）

&emsp;&emsp;那么如何来获取元类对象呢？代码如下：

```
//获取元类对象。
//将类对象当做参数传入，即可获得元类对象(meta-class对象)。
Class objectMetaClass = object_getClass([NSObject class]);
```
&emsp;&emsp;以上代码创建的objectMetaClass就是NSObject的meta-class对象（元类对象）。此外，每个类在内存中有且只有一个meta-class对象。meta-class对象和class对象的内存结构是一样的，但是用途不同。meta-class对象在内存中存储的信息主要包括：

&emsp;&emsp;✅ （1）isa指针

&emsp;&emsp;✅ （2）superclass指针

&emsp;&emsp;✅ （3）类的类方法信息（class method）

&emsp;&emsp;✅ （4）...

meta-class对象的内存存储信息示意图，如下所示

![meta-class对象的内存存储信息示意图](https://upload-images.jianshu.io/upload_images/4164292-c7f00e618e24e465.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'meta-class对象的内存存储信息示意图')

【总结】通过Objective-C 2.0最新苹果源码objc4-750可以一层层探究 struct objc_class 的结构。其实class对象（类对象）和meta-class对象（元类对象）底层实现的数据结构一样的，都是struct objc_class结构体这样的数据结构。区别仅在于meta-class的方法列表(method_list_t * methods) 里存放的是类方法，而不是对象方法，再就是属性列表(property_list_t * properties)里存放的是nil，协议列表(const protocol_list_t * protocols)里存放的也是nil，成员变量列表(const ivar_list_t *ivars)存放的也是nil而已。

struct objc_class在Apple源码objc4-750中的底层实现结构图，如下图所示：

![struct objc_class底层结构示意图](https://upload-images.jianshu.io/upload_images/4164292-f1d74a6969bad946.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'struct objc_class底层结构示意图')

#### 1.4 isa指针

&emsp;&emsp;isa指针作用示意图，如下：

![isa指针作用示意图](https://upload-images.jianshu.io/upload_images/4164292-e4981d0fd687bfb8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'isa指针作用示意图')

&emsp;&emsp;由上图可知，

* （1）instance对象（实例对象）的isa指针指向class对象（类对象）

&emsp;&emsp;当调用“对象方法”（以“-”开头的方法）时，通过instance对象（实例对象）的isa指针找到class对象(类对象)，再找到类对象（class对象）中对象方法的实现，进而调用。

* （2）class对象（类对象）的isa指针指向meta-class对象（元类对象）

&emsp;&emsp;当调用“类方法”（以“+”开头的方法）时，通过class对象（类对象）的isa指针找到meta-class对象（元类对象），最终再找到类方法的实现，进而调用。

#### 1.5 superclass指针

&emsp;&emsp;类对象和元类对象的底层实现的结构体中都有superclass指针,且二者的作用不同，所以我们分开来论述。

##### 1.5.1 class对象（类对象）的superclass

&emsp;&emsp;class对象（类对象）的superclass指针的作用如下：
<br>
&emsp;&emsp;&emsp;&emsp;class对象（类对象）的superclass指针指向其父类的class对象（类对象）。

&emsp;&emsp;在讲解class对象的superclass指针的作用之前，我们先创建两个类：HLPerson和HLStudent。其中HLPerson继承自NSObject类，HLStudent继承自HLPerson类。代码如下：

```
// HLPerson
@interface HLPerson : NSObject <NSCopying>
{
    @public
    int _age;
}
@property (nonatomic, assign) int no;
- (void)personInstanceMethod;
+ (void)personClassMethod;
@end

@implementation HLPerson
- (void)test{
    
}
- (void)personInstanceMethod{
    
}
+ (void)personClassMethod{
    
}
- (id)copyWithZone:(NSZone *)zone{
    return nil;
}
@end

// HLStudent
@interface HLStudent : HLPerson <NSCoding>
{
    int _weight;
}
@property (nonatomic, assign) int height;
- (void)studentInstanceMethod;
+ (void)studentClassMethod;
@end

@implementation HLStudent
- (void)test{
    
}
- (void)studentInstanceMethod{
    
}
+ (void)studentClassMethod{
    
}
- (id)initWithCoder:(NSCoder *)aDecoder{
    return nil;
}

- (void)encodeWithCoder:(NSCoder *)aCoder{
    
}
@end
```
在程序入口main函数中创建HLStudent的实例对象，并调用相关方法。代码如下：

```
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        HLStudent *student = [[HLStudent alloc] init];
 		 //（1）student调用HLStudent类中的对象方法test方法
        [student test];
        //（2）student调用HLPerson类中的对象方法personInstanceMethod方法
        [student personInstanceMethod];
        //（3）student调用NSObject类中的对象方法init方法
        [student init];
        
    }
    return 0;
}
```

*  [student test]; student调用HLStudent类中的对象方法test方法的调用原理如下：首先student的instance对象（实例对象）通过自己的isa指针找到Student的class对象（类对象），然后找到class对象中的对象方法test，进而调用test方法。

* [student personInstanceMethod]; student调用HLPerson类中的对象方法personInstanceMethod方法调用原理如下：首先student实例对象通过自己的isa指针找到Student的class对象（类对象），然后再根据superclass指针找到父类HLPerson的class对象（类对象），再找到HLPerson的class对象中的对象方法personInstanceMethod，进而调用。

* [student init]; student调用NSObject类中的对象方法init方法调用原理是首先student实例对象通过自己的isa指针找到Student的class对象（类对象），然后再根据Student的class对象的superclass指针找到其父类HLPerson的class对象（类对象），然后HLPerson的class对象再根据自身的superclass指针找到其父类NSObject类的class对象（类对象），最后找到NSObject类的class对象中的对象方法init，进行调用。

student实例对象调用方法原理示意图，如下：

![student实例对象调用方法原理示意图](https://upload-images.jianshu.io/upload_images/4164292-c3184916084d8e92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'student实例对象调用方法原理示意图')

【总结】class对象（类对象）的superclass指针指向其父类的class对象（类对象）。

##### 1.5.2 meta-class对象（元类对象）的superclass

&emsp;&emsp;meta-class对象（元类对象）的superclass指针的作用如下：

&emsp;&emsp;&emsp;&emsp;meta-class对象（元类对象）的superclass指针指向其父类的meta-class对象（元类对象）。

```
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        HLStudent *student = [[HLStudent alloc] init];
        //（4）HLStudent调用自身的类方法studentClassMethod
        [HLStudent studentClassMethod];
        //（5）HLStudent调用其父类HLPerson中的类方法personClassMethod
        [HLStudent personClassMethod];
   		 //（6）HLStudent调用其基类NSObject中的类方法load
        [HLStudent load];
        
        [HLStudent abc];
        
    }
    return 0;
}
```

* [HLStudent studentClassMethod]; HLStudent调用自身的类方法studentClassMethod的原理如下：首先通过HLStudent的class对象（类对象）中的isa指针找到HLStudent的meta-class（元类对象），再找到meta-class中的类方法studentClassMethod，进而调用。
<br>
【注意】studentClassMethod方法存储在HLStudent的meta-class对象（元类对象）中。
* [HLStudent personClassMethod]; HLStudent调用其父类HLPerson中的类方法personClassMethod的原理如下：首先通过HLStudent的class对象（类对象）中的isa指针找到HLStudent的meta-class（元类对象），再通过HLStudent的meta-class对象中的superclass指针找到其父类HLPerson的meta-class对象，再找到HLPerson的meta-class中的类方法personClassMethod，进而调用。
<br>
【注意】personClassMethod方法存储在HLPerson的meta-class对象（元类对象）中。
* [HLStudent load]; HLStudent调用其基类NSObject中的类方法load的原理如下：首先通过HLStudent的class对象（类对象）中的isa指针找到HLStudent的meta-class对象（元类对象），再通过HLStudent的meta-class对象中的superclass指针找到其父类HLPerson的meta-class对象，再通过HLPerson的meta-class对象的superclass指针找到基类NSObject的meta-class，最后找到NSObject的meta-class中存储的类方法load，进而调用。
<br>
【注意】load方法存储在基类NSObject的meta-class对象（元类对象）中。

【总结】meta-class对象（元类对象）的superclass指针指向其父类的meta-class对象（元类对象）。

#### 1.6 isa和superclass总结

&emsp;&emsp;下面这张图详细地解释了isa指针和superclass指针各自的作用。

![isa和superclass作用示意图](https://upload-images.jianshu.io/upload_images/4164292-3ebf2e1b0ee4ee5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'isa和superclass作用示意图')

&emsp;&emsp;这张图涉及到三个类，这三个类分别是Subclass(子类)、Super class(父类)、Rootclass(基类)。此外，还清晰地指出了这三个类各自的instance对象、class对象和meta-class对象。此处的Subclass(子类)、Super class(父类)、Rootclass(基类)分别对应上面代码的HLStudent类、HLPerson类和NSObject类。

&emsp;&emsp;上图中，虚线表示isa指向，实线表示superclass指向。

【isa总结】：

* instance(实例对象)的isa指针指向class(类对象)；
* class(类对象)的isa指针指向meta-class(元类对象)；
* 所有的meta-class(元类对象)的isa指针都指向基类的meta-class。

【superclass总结】：

* class(类对象)的superclass指针指向父类的class(类对象)；如果没有父类，那么superclass指针为nil。
* meta-class(元类对象)的superclass指针指向父类的meta-class。特别需要注意的是基类的meta-class(元类对象)的superclass指针指向基类的class对象(类对象)。

##### 1.6.1 instance对象(实例对象)调用对象方法的原理

instance对象(实例对象)调用对象方法的的流程或轨迹是怎样的呢？

&emsp;&emsp;由于对象方法存储在class对象（类对象）的内存中，因此instance对象(实例对象)调用对象方法的原理是首先通过instance(实例对象)的isa指针找到它自己的class对象（类对象），查找自己的class对象中是否存在打算调用的对象方法。如果存在，则进行调用；如果不存在该对象方法，那么就会通过自己class对象中的superclass指针找到它的父类的class对象并查找其中是否存在将要调用的对象方法，如果存在，进行调用；如果不存在，那么就再通过这个父类的class对象的superclass指针，继续往上查找...当查找到基类(NSOject)的class对象（类对象）时，如果此时存在该对象方法就立刻调用，如果基类中也不存在该对象方法，那么意味着自始至终都没有找到该对象方法，此时会报错“unrecognized selector sent to instance”。

【总结】instance对象(实例对象)调用对象方法的的轨迹是先通过isa找到class，并查找是否存在对象方法，如果不存在，就通过superclass指针找到父类。

<!--举个例子，比如HLStudent的实例实例对象student调用某一个对象方法testAbcMthod，代码如下：

```
[student testAbcMthod];
```-->

##### 1.6.2 class对象(类对象)调用类方法的原理

&emsp;&emsp;由于类方法都存储在meta-class对象（元类对象）中，因此class对象(类对象)调用类方法的原理是首先通过class对象的isa指针找到自己的meta-class对象，查看是否存在将要调用的类方法，如果存在，立即调用；如果不存在，则通过该meta-class对象中的superclass指针找到父类的meta-class对象（元类对象）并查找其中是否存在将要调用的类方法，如果存在，立刻调用，如果不存在，则通过superclass指针继续一层一层往上查找，直至找到基类(Root class)的元类对象，并查找基类(Root class)的元类对象中是否存在将要调用类方法，如果存在立即调用，如果也不存在，此时并不会报“unrecognized selector sent to instance”错误，而是会继续通过superclass指针找到基类的class对象（类对象），如果基类的class对象中存在将要调用的类方法就立刻调用，如果不存在，此时才会报错(“unrecognized selector sent to instance”)。

【总结】class调用类方法的轨迹是：先通过isa指针找到meta-class，并查找类方法是否存在，如果不存在，那么就通过superclass指针一层一层往上往上查找父类。


#### 1.7 isa底层实现细节

&emsp;&emsp;我们知道，某个类的instance对象（实例对象）的isa指针指向该类的class对象（类对象），那么实际上真的是isa指针直接指向class对象吗？

&emsp;&emsp;答案“不是”。对于64bit设备来说，instance对象的isa指针并非直接指向class对象，因为从64bit开始，isa需要进行一次位运算，也就是“& ISA_MASK”，才能计算出真实地址（而对于32bit设备来说，instance对象的isa指针直接指向的就是class对象）。同样地，class对象的isa地址值进行一次位运算“& ISA_MASK”,得到的就是meta-class的地址值。

&emsp;&emsp;而某个类的class对象（类对象）的superclass指针是直接指向该类的父类的类对象的，不存在“& ISA_MASK”这样的位运算。


