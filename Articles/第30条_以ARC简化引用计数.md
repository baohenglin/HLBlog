# 第30条 以ARC简化引用计数

&emsp;&emsp;Clang编译器项目带有一个“静态分析器”(static analyzer)，用于指明程序里引用计数出问题的地方。假设下面这段代码采用手动方式管理引用计数：

```
if([self shouldLogMessage]) {
	NSString *message = [[NSString alloc] initWithFormat:@"I am object,%p",self];
	NSLog(@"message = %@",message);
}
```

&emsp;&emsp;此代码有内存泄漏问题，因为if语句块末尾并未释放message对象。由于在if语句之外无法引用message，所以此对象所占的内存泄漏了（没有正确释放已经不再使用的内存）。判定内存是否泄漏所用的规则很简明：调用NSString的alloc方法所返回的那个message对象的引用计数比期望值要多1，然而却没有与之对应的释放操作来抵消。分析出有内存泄漏问题的对象，这正是“静态分析器”要做的事情。此外，静态分析器还有更为深入的用途。既然可以查明内存管理问题，那么应该也可以根据需要，预先加入适当的保留或释放操作以避免这些问题。自动引用计数这一思路正是源于此。自动引用计数所做的事情与其名称相符，就是自动管理引用计数。于是，在前面那段代码的if语句块结束之前，可以于message对象上自动执行release操作，也就是把代码自动修改为以下代码：

```
if([self shouldLogMessage]) {
	NSString *message = [[NSString alloc] initWithFormat:@"I am object,%p",self];
	NSLog(@"message = %@",message);
	[message release];//Added by ARC
}
```

&emsp;&emsp;使用ARC时，引用计数实际上还是要执行的，只不过保留与释放操作现在是由ARC自动为对象添加的。ARC是基于核心的内存管理语义而构建的，这套标准语义贯穿于整个Objective-C语言。

&emsp;&emsp;由于ARC会自动执行retain、release、autorelease等操作，所以直接在ARC下调用这些内存管理方法是非法的。也就是在ARC环境下，不能手动调用retain、release、autorelease、dealloc等方法。否则会产生编译错误。

&emsp;&emsp;实际上，ARC在调用retain、release、autorelease等操作方法时，并不是通过普通的Objective-C消息派发机制，而是直接调用其底层C语言版本。这样做性能更好，因为保留及释放操作需要频繁执行，所以直接调用底层函数能节省很多CPU周期。比方说，ARC会调用与retain等价的底层函数objc_retain。这也是不能覆写retain、release和autorelease的缘由，因为这些方法从来不会被直接调用。