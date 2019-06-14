## 以ARC简化引用计数

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

## 使用ARC时必须遵循的方法命名规则

&emsp;&emsp;将内存管理语义在方法名中表示出来早已成为Objective-C的惯例，而ARC则将之确立为硬性规定。这些规则简单地体现在方法名上。若方法名以下列词语开头，则其返回的对象归调用者所有。这些词语包括：

* alloc
* new
* copy
* mutableCopy

&emsp;&emsp;归调用者所有的意思是：调用上述四种方法的那段代码要负责释放方法所返回的对象。也就是说，这些对象的引用计数是正值，而调用了这四种方法的那段代码要将其中一次保留操作抵消掉。如果还有其他对象保留此对象，并对其调用了autorelease，那么保留计数的值可能比1大，这也是retainCount方法不太有用的原因之一（参见第36条）。

&emsp;&emsp;若方法名不以上述四个词语开头，则表示其返回的对象并不归调用者所有。在这种情况下，返回的对象会自动释放，所以其值在跨越方法调用边界后依然有效。要想使对象多存活一段时间，必须令调用者保留它才行。维系这些规则所需的全部内存管理事宜均由ARC自动处理，其中也包括在将要返回的对象上调用autorelease

&emsp;&emsp;除了会自动调用“保留”与“释放”方法外，使用ARC还有其他好处，它可以执行一些手动操作很难甚至无法完成的优化。例如在编译期，ARC会把能够互相抵消的retain、release、autorelease操作约简。如果发现在同一个对象上执行了多次“保留”与“释放”操作，那么ARC有时可以成对地移除这两个操作。

&emsp;&emsp;将内存管理交由编译器和运行期组件来完成，可以使代码得到多种优化。所以推荐使用ARC。

## 变量的内存管理语义

&emsp;&emsp;ARC也会处理局部变量与实例变量的内存管理。默认情况下，每个变量都是指向对象的强引用。ARC在处理setter方法时，会先保留新值，再释放旧值，最后设置实例变量。代码如下：

```
- (void)setObject:(id)object {

   [object retain];// Added by ARC
   [_object release];// Added by ARC

   _object = object; 
}
```
如果先release，再retain，那么该对象可能已经被系统回收，实例变量变成了悬挂指针（指针指向nil的指针），此时retain操作无效，应用程序会崩溃。

&emsp;&emsp;在应用程序中，可用下列修饰符来改变局部变量与实例变量的语义：

|修饰符|语义|
|:--:|:--:|
|__strong|默认语义，强持有，保留此值|
|__weak|不保留此值，安全。对象释放后，指针自动置nil|
|__unsafe_unretained|不保留此值，不安全。对象释放后，指针依然指向原地址（不会自动置为nil）|
|__autoreleasing|此值在方法返回时自动释放|

我们经常给局部变量加上修饰符，用以打破由“block”所引入的“保留环”（retain cycle），block会自动保留其所捕获的全部对象，而如果这其中有某个对象又保留了block本身，那么就可能导致保留环。可以用__weak局部变量来打破这种“保留环”。

## ARC如何清理实例变量




