iOS之内存管理分析



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

解决方法1：使用scheduledTimerWithTimeInterval: repeats: block:方法

//打破循环引用的原因：self->(强引用)timer->(强引用)block->(弱引用)self

```
//解决方法1：使用scheduledTimerWithTimeInterval: repeats: block:替代scheduledTimerWithTimeInterval: target: selector: userInfo: repeats:
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
//解决方法2：
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

方法2和方法3乍看起来好像差不多，其实，方法3与方法2相比，执行效率更高。因为NSProxy与NSObject类是同级别的类，都是基类。NSProxy就是专门用来做消息转发的，在调用方法过程中，如果当前类中没有查找到要调用的方法，那么不会一层一层去父类当中去查找是否存在该方法，也不会进入消息动态解析阶段，而是直接进入到消息转发阶段，其执行效率更高，所以推荐使用方法3这种通过NSProxy类创建代理对象的解决方法。

## 内存管理总结：

1. 使用CADisplayLink、NSTimer有什么注意点？

CADispalyLink、NSTimer会对target产生强引用，如果target又对它们产生强引用，那么就会导致循环引用。



2.简述内存的几大区域

3.谈谈对iOS内存管理的理解

4.autorelease在什么时候会被释放？

5.方法里有局部对象，出了方法后会立即释放吗？

6.ARC都帮我们做了什么？

7.weak指针的实现原理？
