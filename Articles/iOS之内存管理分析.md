
## iOS程序的内存布局

iOS程序内存布局从低地址到高地址依次是保留段(保留着供系统使用)、代码段(_TEXT)、数据段(_DATA)、堆(heap)、栈(stack)和内核区。

iOS程序的内存布局如下图所示：

![iOS程序的内存布局示意图.png](https://upload-images.jianshu.io/upload_images/4164292-fc62e98035ff4f0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 代码段：编译之后的代码
* 数据段：主要存放字符串常量(比如NSString *str = @"123")、已初始化数据(已初始化的全局变量、静态变量等)、未初始化数据(未初始化的全局变量、静态变量等)
* 栈：函数调用开销，比如局部变量。分配的内存空间地址越来越小。
* 堆：通过alloc、malloc、calloc等动态分配的空间，分配的内存空间地址越来越大。


## Tagged Pointer

从64bit开始，iOS引入了Tagged Pointer技术，用于优化NSNumber、NSDate、NSString等小对象的存储。在没有使用Tagged Pointer之前，NSNumber等对象需要动态分配内存、维护引用计数等，NSNumber指针存储的是堆中NSNumber对象的地址值。使用Tagged Pointer之后，NSNumber指针里面存储的数据变成了：Tag + Data，也就是将数据直接存储在了指针中。当指针不够存储数据时，才会使用动态分配内存的方式来存储数据。objc_msgSend能识别Tagged Pointer，比如NSNumber的intValue方法，直接从指针提取数据，节省了以前的调用开销。

如何判断一个指针是否为Tagged Pointer？

在mac平台上，如果指针的最低有效位是1，那么该指针为Tagged Pointer，否则就是对象。在iOS平台上，如果指针的最高有效位是1（第64bit），那么该指针为Tagged Pointer。

苹果源码中判断指针是否为Tagged Pointer的逻辑如下图所示：

![判断指针是否为TaggedPointer的逻辑示意图.png](https://upload-images.jianshu.io/upload_images/4164292-0c1bf71d233b6f48.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Tagged Pointer的相关题目

思考以下2段代码会发生什么事？有什么区别？

代码A:

```
dispatch_queue_t queue = dispatch_get_global_queue(0, 0);
    for (int i = 0; i < 1000; i++) {
        dispatch_async(queue, ^{
            //加锁
            self.name = [NSString stringWithFormat:@"abcdefghijk"];
            //解锁
        });
    }
```

代码B：

```
dispatch_queue_t queue = dispatch_get_global_queue(0, 0);
    for (int i = 0; i < 1000; i++) {
        dispatch_async(queue, ^{
            //加锁
            self.name = [NSString stringWithFormat:@"abc"];
            //解锁
        });
    }
```

代码A会崩溃，代码B不会崩溃。代码A崩溃是因为在Setter方法中，由于多条线程对 _name成员变量进行了不止一次Release操作。代码B中的self.name是Tagged Pointer，不是OC对象，不会像普通的OC对象那样调用setter方法执行retain、release操作，而是直接将地址值赋值给了self.name,所以不会崩溃。

## OC对象的内存管理

在iOS中，使用引用计数来管理OC对象的内存。一个新创建的OC对象引用计数默认是1，当引用计数减为0，OC对象就会销毁，释放其占用的内存空间。调用retain会让OC对象的引用计数+1，调用release会让OC对象的引用计数-1。

当调用alloc、new、copy、mutableCopy方法返回了一个对象，在不需要这个对象时，要调用release或者autorelease来释放它。

## copy VS MutableCopy、浅拷贝 VS 深拷贝

copy的目的是为了产生一个副本对象，和源对象互补影响。也就是改变了源对象，不会影响副本对象；修改了副本对象，不会影响源对象。

iOS提供了2个拷贝方法copy和mutableCopy，其中copy是不可变拷贝，产生的是不可变副本；mutableCopy是可变拷贝，产生的是可变副本。

* 浅拷贝：指针拷贝，不拷贝内容，没有产生新对象，只是使当前对象引用计数加1。

* 深拷贝：内容拷贝，产生了新对象。比如mutableCopy

![copy和mutableCopy.png](https://upload-images.jianshu.io/upload_images/4164292-38d2c5d8f24c6269.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

【总结】如果源对象是不可变对象，那么该对象调用copy是浅拷贝，调用mutableCopy是深拷贝。**如果源对象是可变对象，那么不管调用copy还是mutableCopy，都是深拷贝**。
       
       
## 属性修饰词assign/retain/strong/copy

assign：修饰基本数据类型，在生成的setter内直接赋值

retain：在生成的setter内将新值retain保留，将旧值release释放掉。

strong：数组和字典类型的属性一般用strong修饰。

copy：在生成的setter内新值调用copy，将旧值release释放掉。copy修饰的属性一定要是不可变的对象，否则可能会出错。一般来说字符串类型的属性都是用copy来修饰。

## 引用计数的存储

在64bit中，引用计数可以直接存储在优化过的isa指针中，但是如果isa指针中的19bit存储空间存储不下时，就存储在SideTable类中 RefcountMap refcnts散列表中。

```
struct SideTable {
    spinlock_t slock;
    RefcountMap refcnts;//存放着对象引用计数的散列表
    weak_table_t weak_table;
}
```

## weak指针的实现原理

修饰指针的修饰词包括__strong、__weak、__unsafe_unretained这三个修饰词。

* __strong：强引用
* __weak：弱引用，当指针指向的对象销毁之后，会将该指针置为nil，防止产生野指针
* __unsafe_unretained：弱引用，当指针指向的对象销毁之后，不会将该指针置为nil，会产生野指针，不安全。

那么__weak的实现原理是怎样的呢？

我们可以通过查看objc源码来研究__weak的实现原理。(objc->Source->NSObject.mm->dealloc)

__weak的实现原理:程序运行过程中Runtime将对象的弱引用存储到弱引用哈希表weak_table_t weak_table中，当弱引用指针指向的对象销毁时，通过相应计算获取当前对象所对应的弱引用表，并将弱引用表中存储的所有弱引用都清除掉，也就是置为nil。

## autoreleasepool的实现原理

```
@autoreleasepool {
        
}
```

自动释放池的主要底层数据结构是：__AtAutoreleasePool、AutoreleasePoolPage。调用了autorelease的对象最终都是通过AutoreleasePoolPage对象来管理的。

AutoreleasePoolPage的底层结构的成员变量示意图如下：

![AutoreleasePoolPage底层结构.png](https://upload-images.jianshu.io/upload_images/4164292-c029ba2ea0b8082d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

各个成员变量的含义如下：

* magic:用来检验AutoreleasePoolPage的结构是否完整
* next:指向最新添加的autorelease对象的下一个位置
* thread:指向当前线程
* parent：指向父节点，第一个节点的parent值为nil
* child:指向子节点，最后一个节点的child值为nil
* depth:代表深度，从0开始，往后递增1
* hiwat:表示high water mark

AutoreleasePoolPage的底层详细结构示意图如下：

![AutoreleasePoolPage的详细结构示意图.png](https://upload-images.jianshu.io/upload_images/4164292-010118bb15bff331.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


每个AutoreleasePoolPage对象占用4096字节内存，除了用来存放它内部的成员变量，剩下的空间用来存放autorelease对象的地址。其中的child变量存储着下一个AutoreleasePoolPage的地址值，parent变量存储着上一个AutoreleasePoolPage的地址值。所有的AutoreleasePoolPage对象通过双向链表的形式连接在一起。

autoreleasepool的本质是在花括号的开头调用了 atautoreleasepoolobj = objc_autoreleasePoolPush();函数，在花括号的结尾处调用了objc_autoreleasePoolPop(atautoreleasepoolobj);函数。

调用objc_autoreleasePoolPush方法时会将一个POOL_BOUNDARY压入栈中作为边界标识，并返回其存放的内存地址。此外会将所有的autorelease对象(对象调用了autorelease方法)的地址值依次存放在接下来的地址空间中，当一个AutoreleasePoolPage的空间被占满时，就会创建下一个AutoreleasePoolPage对象，通过parent和child指针连接成双向链表。

当调用objc_autoreleasePoolPop函数时，将当初入栈时存放的POOL_BOUNDARY传入Pop函数，并从最后一个入栈（栈顶）的autorelease对象开始依次对它们调用release方法，直到POOL_BOUNDARY这个边界为止。

## RunLoop和Autorelease

打印主线程的RunLoop

```
NSLog(@"%@",[NSRunLoop mainRunLoop]);
```

从打印的结果，我们可以发现以下两个与自动释放池相关的observer：

```
"<CFRunLoopObserver 0x6000012705a0 [0x1042deae8]>{valid = Yes, activities = 0x1, repeats = Yes, order = -2147483647, callout = _wrapRunLoopWithAutoreleasePoolHandler (0x106dd987d), context = <CFArray 0x600002d64cc0 [0x1042deae8]>{type = mutable-small, count = 1, values = (\n\t0 : <0x7ffc62802058>\n)}}",
 
 "<CFRunLoopObserver 0x600001270460 [0x1042deae8]>{valid = Yes, activities = 0xa0, repeats = Yes, order = 2147483647, callout = _wrapRunLoopWithAutoreleasePoolHandler (0x106dd987d), context = <CFArray 0x600002d64cc0 [0x1042deae8]>{type = mutable-small, count = 1, values = (\n\t0 : <0x7ffc62802058>\n)}}"
```

iOS在主线程的Runloop中注册了2个Observer，第1个Observer监听了kCFRunLoopEntry事件，会调用objc_autoreleasePoolPush()。第2个Observer一方面监听了kCFRunLoopBeforeWaiting事件，会调用objc_autoreleasePoolPop()、objc_autoreleasePoolPush()；另一方面监听了kCFRunLoopBeforeExit事件，会调用objc_autoreleasePoolPop()。其实autoreleasepool对象就是在当前的RunLoop进入休眠（kCFRunLoopBeforeWaiting）之前调用objc_autoreleasePoolPop()的时候被release的。

## 内存管理总结：

1. 使用CADisplayLink、NSTimer有什么注意点？

CADispalyLink、NSTimer会对target产生强引用，如果target又对它们产生强引用，那么就会导致循环引用。另一个问题就是可能存在定时器不准时的问题。



2.简述内存的几大区域

3.谈谈对iOS内存管理的理解

4.autorelease对象在什么时候会被release的？

iOS在主线程的Runloop中注册了2个Observer，第1个Observer监听了kCFRunLoopEntry事件，会调用objc_autoreleasePoolPush()。第2个Observer一方面监听了kCFRunLoopBeforeWaiting事件，会调用objc_autoreleasePoolPop()、objc_autoreleasePoolPush()；另一方面监听了kCFRunLoopBeforeExit事件，会调用objc_autoreleasePoolPop()。其实autoreleasepool对象就是在当前的RunLoop进入休眠（kCFRunLoopBeforeWaiting）之前调用objc_autoreleasePoolPop()的时候被release的。

5.方法里有局部对象，出了方法后会立即释放吗？

6.ARC都帮我们做了什么？

ARC是LLVM和Runtime系统相互协作的一个结果。开启ARC后，LLVM编译器自动添加了retain、release等有关内存管理的代码。此外，程序运行过程中Runtime将使用__weak修饰的对象的弱引用存储到弱引用哈希表weak_table_t weak_table中，当弱引用指针指向的对象销毁时，通过相应计算获取当前对象所对应的弱引用表，并将弱引用表中存储的所有弱引用都清除掉，也就是置为nil。


7.weak指针的实现原理？

程序运行过程中Runtime将对象的弱引用存储到弱引用哈希表weak_table_t weak_table中，当弱引用指针指向的对象销毁时，通过相应计算获取当前对象所对应的弱引用表，并将弱引用表中存储的所有弱引用都清除掉，也就是置为nil。



## NSTimer、CADisplayLink的循环引用问题

```
@interface ViewController ()
@property (nonatomic, strong) CADisplayLink *link;
@property (nonatomic, strong) NSTimer *timer;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    //CADisplayLink，也是一种定时器，保证调用频率和屏幕的刷帧频率一致，60FPS
    //CADisplayLink定时器也会产生循环引用
    self.link = [CADisplayLink displayLinkWithTarget:self selector:@selector(linkTest)];
    [self.link addToRunLoop:[NSRunLoop mainRunLoop] forMode:NSDefaultRunLoopMode];
    
    //使用scheduledTimerWithTimeInterval: target: selector: userInfo: repeats:会产生循环引用。
    //__weak typeof(self) weakSelf = self;//不能解决NSTimer循环引用问题。可以解决block的循环引用。
    self.timer = [NSTimer scheduledTimerWithTimeInterval:1.0 target:self selector:@selector(timerTest) userInfo:nil repeats:YES];
    
}
- (void)linkTest
{
    NSLog(@"%s",__func__);
}
- (void)timerTest
{
    NSLog(@"%s",__func__);
}
- (void)dealloc
{
    NSLog(@"%s",__func__);
    //定时器都要调用invalidate方法来销毁定时器，否则会崩溃
    [self.link invalidate];
    [self.timer invalidate];
}
@end
```

以上代码中定时器CADisplayLink和NSTimer都产生循环引用的问题，原因是CADisplayLink、NSTimer会对target产生强引用，同时target又对它们产生强引用，从而导致了循环引用。分析如下：

* self->(强引用)link ->(强引用)target:self。
* self->(强引用)timer->(强引用)target:self。

解决方法1：使用scheduledTimerWithTimeInterval: repeats: block:方法替代scheduledTimerWithTimeInterval: target: selector: userInfo: repeats:方法

//打破循环引用的原因：self->(强引用)timer->(强引用)block->(弱引用)self

```
__weak typeof(self) weakSelf = self;
self.timer = [NSTimer scheduledTimerWithTimeInterval:1.0 repeats:YES block:^(NSTimer * _Nonnull timer) {
    [weakSelf timerTest];
}];
```

解决方法2：自定义中间代理类+消息转发机制

创建一个基于NSObject的类HLproxy

HLproxy.h代码如下：

```
@interface HLproxy : NSObject
+ (instancetype)proxyWithTarget:(id)target;
@property (nonatomic, weak) id target;
@end
```

HLproxy.m代码如下：

```
#import "HLproxy.h"
@implementation HLproxy
+ (instancetype)proxyWithTarget:(id)target
{
    HLproxy *proxy = [[HLproxy alloc]init];
    proxy.target = target;
    return proxy;
}
/**
 消息转发机制的三大阶段：(1)消息发送阶段,(2)动态方法解析阶段,(3)消息转发阶段
 */
//消息转发阶段
- (id)forwardingTargetForSelector:(SEL)aSelector
{
    //objc_msgSend(self.target,aSelector);
    return self.target;
}
//- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector
//{
//    return nil;
//}
//- (void)forwardInvocation:(NSInvocation *)anInvocation
//{
//    
//}
@end
```

ViewController.m中代码做如下修改：

```
self.timer = [NSTimer scheduledTimerWithTimeInterval:1.0 target:[HLproxy proxyWithTarget:self] selector:@selector(timerTest) userInfo:nil repeats:YES];
```

同理，CADisplayLink定时器的循环引用问题也可以通过方法2来解决

```
self.link = [CADisplayLink displayLinkWithTarget:[HLproxy proxyWithTarget:self] selector:@selector(linkTest)];
```

解决方法3：使用代理对象(NSProxy)

创建继承自NSProxy的类HLBestProxy

HLBestProxy.h代码如下：

```
//继承自 NSProxy
@interface HLBestProxy : NSProxy
+ (instancetype)proxyWithTarget:(id)target;
@property (nonatomic, weak) id target;
@end
```

HLBestProxy.m代码如下：

```
#import "HLBestProxy.h"
@implementation HLBestProxy
+ (instancetype)proxyWithTarget:(id)target
{
    //NSProxy没有init方法，不需要调用init。
    HLBestProxy *proxy = [HLBestProxy alloc];
    proxy.target = target;
    return proxy;
}
//如果当前类中没有查找到要调用的方法，那么不会去父类当中去查找该方法，也不会进入消息动态解析阶段，而是直接进入到消息转发阶段。其执行效率更高。
//NSProxy与NSObject类是同级别的，都是基类。NSProxy是专门用来做消息转发的，
- (NSMethodSignature *)methodSignatureForSelector:(SEL)sel
{
    //返回 方法签名
    return [self.target methodSignatureForSelector:sel];
}
- (void)forwardInvocation:(NSInvocation *)invocation
{
    //调用方法
    [invocation invokeWithTarget:self.target];
}
@end
```

方法2和方法3乍看起来好像差不多，其实，方法3与方法2相比，执行效率更高。因为NSProxy与NSObject类是同级别的类，都是基类。NSProxy是专门用来做消息转发处理的类，在调用方法过程中，如果当前类HLBestProxy中没有查找到要调用的方法，那么它不会去其父类NSProxy当中去查找是否存在该方法，也不会进入消息动态解析阶段，而是直接进入到消息转发阶段，其执行效率更高，所以推荐使用方法3这种通过NSProxy类创建代理对象的解决方法。

## NSProxy

NSProxy的底层实现源码可以查看[GNUstep Base](http://www.gnustep.org/resources/downloads.php)Foundation中的NSProxy.m中的源码。

下面代码输出结果是什么？

```
ViewController *vc = [[ViewController alloc]init];
HLproxy *proxy1 = [HLproxy proxyWithTarget:vc];
HLBestProxy *proxy2 = [HLBestProxy proxyWithTarget:vc];
NSLog(@"%d %d",[proxy1 isKindOfClass:[ViewController class]],[proxy2 isKindOfClass:[ViewController class]]);
```

结果是：

```
2019-07-03 11:59:31.515103+0800 内存管理_NSTimer[47838:2484092] 0 1
```

我们看到[proxy2 isKindOfClass:[ViewController class]]的结果是1，为什么会这样呢？因为proxy2的类对象HLBestProxy继承自NSProxy，HLBestProxy内部自动实现了消息转发操作。

```
- (NSMethodSignature *)methodSignatureForSelector:(SEL)sel
{
    //返回 方法签名
    return [self.target methodSignatureForSelector:sel];
}
- (void)forwardInvocation:(NSInvocation *)invocation
{
    //调用方法
    [invocation invokeWithTarget:self.target];
}
```
最终代码[proxy2 isKindOfClass:[ViewController class]]转化为[vc isKindOfClass:[ViewController class]]，所以结果是1。


## GCD定时器

由于NSTimer定时器依赖于RunLoop，如果RunLoop的任务过于繁重，那么可能将导致NSTimer定时器不准时。那么怎么做才能保证定时器准时呢？

可以使用**GCD定时器**,GCD定时器是跟系统内核直接挂钩，不依赖于RunLoop，所以GCD定时器非常准时。

```
- (void)viewDidLoad {
    [super viewDidLoad];
    NSLog(@"----begin");
    //串行队列
    dispatch_queue_t queue = dispatch_queue_create("timer", DISPATCH_QUEUE_SERIAL);
    //创建GCD定时器
    dispatch_source_t timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue);
    //设置时间
    NSTimeInterval start = 2.0;//2秒后开始执行
    NSTimeInterval interval = 1.0;//每隔一秒执行
    dispatch_source_set_timer(timer, dispatch_time(DISPATCH_TIME_NOW, start * NSEC_PER_SEC), interval * NSEC_PER_SEC, 0);
    //设置回调
    dispatch_source_set_event_handler(timer, ^{
        NSLog(@"每隔1秒打印---1111");
    });
    //启动定时器
    dispatch_resume(timer);
    self.timer = timer;
}
```

由于项目中可能有多出需要使用该GCD定时器，所以我们可以将GCD定时器封装成专门的定时器类。[GCD定时器完整封装](https://github.com/baohenglin/GCD-Timer)
