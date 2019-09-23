Objective-C与C++、Java等面向对象语言类似，不过很多方面有所差别。Objective-C语言使用“消息结构”(messaging structure)而非“函数调用”(function calling)。消息与函数调用之间的区别看上去就像这样：

```
//Messaging (Objective-C)
Object *obj = [Object new];
[obj performWith:parameter1 and:parameter2];

//Function calling (C++)
Object *obj = new Object;
obj -> perform(parameter1, parameter2);
```

关键区别在于：使用消息结构的语言，其运行时所应执行的代码由运行环境决定；而使用函数调用的语言，则由编译器决定。如果范例代码中调用的函数是多态的，那么在运行时就要按照“虚方法表”(virtual table)来查出到底应该执行哪个函数实现。而采用消息结构的语言，不论是否多态，总是在运行时才会去查找所要执行的方法。实际上，编译器甚至不关心接收消息的对象是何种类型。接收消息的对象问题也要在运行时处理，其过程叫做“动态绑定”（dynamic binding），第11条会详细论述其细节。

Objective-C的重要工作都由“运行期组件”（runtime component）而非编译器来完成。使用 Objective-C的面向对象特性所需的全部数据结构及函数都在运行期组件里面。举例来说，运行期组件中的含有全部内存管理方法。运行期组件本质上就是一种与开发者所编代码相链接的“动态库”（dynamic library），其代码能把开发者编写的所有程序粘合起来。

Objective-C是 C 的“超集”（superset），所以C语言中的所有功能在编写Objective-C代码时依然适用。Objective-C语言中的指针是用来指示对象的。想要声明一个变量，令其指代某个对象，可用如下语法：

```
NSString * someString = @"The string";
```

它声明了一个名为 someString 的变量，其类型是 NSString* 。也就是说，此变量为指向 NSString的指针。所有 Objective-C语言的对象都必须这样声明，因为对象所占内存总是分配在“堆空间”（heap space）中，而绝不会分配在“栈”（stack）上。

someString变量指向分配在堆里的某块内存，其中含有一个NSString对象。也就是说，如果再创建一个变量，令其指向同一地址，那么并不拷贝该对象，只是这两个变量会同时指向此对象。

```
NSString *someString = @"The string";
NSString *anotherString = someString;
```

只有一个NSString实例，然而有两个变量指向此实例。两个变量都是 NSString * 型，这说明当前“栈帧”（stack frame）里分配了两块内存，每块内存的大小都能容下一枚指针（在32位架构的计算机上是4字节，64位计算机上是8个字节）。这两块内存里的值都一样，就是 NSString 实例的内存地址。

分配在堆中的内存必须直接管理，而分配在栈上用于保存变量的内存则会在其栈帧弹出时自动清理。在Objective-C代码中，有时会遇到定义里不含 * 的变量，它们可能会使用“栈空间”（stack space）。这些变量保存的不是 Objective-C对象。比如 CoreGraphics框架中的 CGRect就是个例子：CGRect frame; CGRect是C结构体，其定义是：

```
struct CGRect {
  CGPoint origin;
  CGSize size;
};
typedef struct CGRect CGRect;
```

整个系统框架都在使用这种结构体，因为如果改用 Objective-C 对象来做的话，性能会受影响。与创建结构体相比，创建对象还需要额外开销，例如分配及释放堆内存等。如果只需保存 int、float、double、char等“非对象类型”（nonobject type），那么通常使用 CGRect这种结构体就可以了。



