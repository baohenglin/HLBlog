# RunLoop


RunLoop顾名思义也就是运行循环，在程序运行过程中循环执行某些任务。RunLoop的应用范畴包括以下几个方面：

* 定时器(Timer)、PerformSelector
* GCD Async Main Queue
* 事件响应、手势识别、界面刷新
* 网络请求
* AutoreleasePool

如果没有RunLoop的话，程序执行完之后就会立即退出。而使用RunLoop后，程序执行完成后并不会马上退出，而是保持运行状态。比如iOS项目中的main函数中UIApplicationMain函数内部就实现了RunLoop。

```
int main(int argc, char * argv[]) {
    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```

UIApplicationMain内部逻辑的伪代码大致如下：

```
int main(int argc, char * argv[]) {
    @autoreleasepool {
        int retVal = 0;
        do {
            //休眠中等待消息
            int message = sleep_and_wait();
            //RunLoop唤醒线程，处理消息
            retVal = process_message(message);
        } while (0 == retVal);
        return 0;
    }
}
```

综上所述，RunLoop的基本作用是：

* 保持程序的持续运行；
* 处理App中的各种事件（比如触摸事件、定时器事件等）；
* 节省CPU资源，提高程序性能：有待执行任务时执行任务，不执行任务时休眠。

## RunLoop对象

首先，iOS 开发中能遇到两个线程对象: pthread_t 和 NSThread。pthread_t 和 NSThread 是一一对应的。比如，你可以通过 pthread_main_thread_np() 或 [NSThread mainThread] 来获取主线程；也可以通过 pthread_self() 或 [NSThread currentThread] 来获取当前线程。

iOS中有2套API来访问和使用RunLoop对象。其中一套基于OC的Foundation:NSRunLoop，另一套是基于C语言的Core Foundation:CFRunLoopRef，CFRunLoop 是基于 pthread 来管理的。其中的NSRunLoop和CFRunLoopRef都代表着RunLoop对象。NSRunLoop是基于CFRunLoopRef的一层OC包装(这也是使用OC方式和C语言方式打印出来的主线程不同的原因，主线程真正的地址是使用C语言打印出来的地址)。CFRunLoopRef是开源的。[CFRunLoopRef下载链接](https://opensource.apple.com/tarballs/CF/)

获取当前线程的RunLoop对象：

```
//OC方法，获取当前线程的RunLoop对象
NSRunLoop *runloop = [NSRunLoop currentRunLoop];
//C语言
CFRunLoopRef runloop2 = CFRunLoopGetCurrent();

```

获取主线程的RunLoop对象：

```
NSRunLoop *runloop = [NSRunLoop mainRunLoop];
```

## RunLoop与线程的关系

获取主线程对应的RunLoop的源码如下所示：

```
CFRunLoopRef CFRunLoopGetMain(void) {
    CHECK_FOR_FORK();
    static CFRunLoopRef __main = NULL; // no retain needed
    if (!__main) __main = _CFRunLoopGet0(pthread_main_thread_np()); // no CAS needed
    return __main;
}
```

获取当前线程对应的RunLoop的源码如下：

```
CFRunLoopRef CFRunLoopGetCurrent(void) {
    CHECK_FOR_FORK();
    CFRunLoopRef rl = (CFRunLoopRef)_CFGetTSD(__CFTSDKeyRunLoop);
    if (rl) return rl;
    return _CFRunLoopGet0(pthread_self());
}
```

其中 CFRunLoopGetMain 和 CFRunLoopGetCurrent 中都调用了_CFRunLoopGet0()函数，_CFRunLoopGet0 函数的底层实现源码如下：

```
//声明一个全局的可变字典 __CFRunLoops 用于存储每一条线程以及和该线程对应的RunLoop对象 ，key是线程pthread_t，value是RunLoop对象
static CFMutableDictionaryRef __CFRunLoops = NULL;
// loopsLock 用于访问 __CFRunLoops 时加锁，锁的目的是:保证线程读写安全，防止多条线程同时访问__CFRunLoops对应的内存空间
static CFLock_t loopsLock = CFLockInit;

// should only be called by Foundation
// t==0 is a synonym(代名词) for "main thread" that always works
//获取 RunLoop函数
CF_EXPORT CFRunLoopRef _CFRunLoopGet0(pthread_t t) {
    //kNilPthreadT表示空的线程
    //判断t所指向的线程是否为空，如果为空，就获取当前的主线程进行赋值
    if (pthread_equal(t, kNilPthreadT)) {
	t = pthread_main_thread_np();
    }
    //处理线程安全，加锁
    __CFLock(&loopsLock);
    //判断全局的可变字典__CFRunLoops是否为空
    if (!__CFRunLoops) {
        //解锁
        __CFUnlock(&loopsLock);
    //定义一个局部可变字典 dict
	CFMutableDictionaryRef dict = CFDictionaryCreateMutable(kCFAllocatorSystemDefault, 0, NULL, &kCFTypeDictionaryValueCallBacks);
    //创建一个与主线程对应的RunLoop对象mainLoop
	CFRunLoopRef mainLoop = __CFRunLoopCreate(pthread_main_thread_np());
    //将主线程以及和主线程对应的RunLoop对象mainLoop存储到字典dict中
	CFDictionarySetValue(dict, pthreadPointer(pthread_main_thread_np()), mainLoop);
    //OSAtomicCompareAndSwap**[Barrier](type __oldValue, type __newValue, volatile type *__theValue)：这组函数用于比较__oldValue是否与__theValue指针指向的内存位置的值匹配，如果匹配，则将__newValue的值存储到__theValue指向的内存位置。
    //将创建的局部可变字典 dict 赋值给全局字典__CFRunLoops
	if (!OSAtomicCompareAndSwapPtrBarrier(NULL, dict, (void * volatile *)&__CFRunLoops)) {
        //赋值成功后，release局部可变字典dict
	    CFRelease(dict);
	}
    //将mainLoop成功存储到字典__CFRunLoops后对mainLoop执行一次release操作（因为mainLoop存储到字典后会自动执行一次retain操作。）
	CFRelease(mainLoop);
        __CFLock(&loopsLock);
    }
    //从全局的字典__CFRunLoops中获取对应于当前线程的RunLoop
    CFRunLoopRef loop = (CFRunLoopRef)CFDictionaryGetValue(__CFRunLoops, pthreadPointer(t));
    //解锁
    __CFUnlock(&loopsLock);
    //如果获取不到RunLoop，那么就重新创建newLoop，并存储到字典__CFRunLoops中(RunLoop 的创建发生在第一次获取该线程对应的RunLoop时)
    if (!loop) {
    //根据当前线程t创建一个RunLoop
	CFRunLoopRef newLoop = __CFRunLoopCreate(t);
        __CFLock(&loopsLock);
    //再一次从全局字典__CFRunLoops中获取对应于当前线程的RunLoop
	loop = (CFRunLoopRef)CFDictionaryGetValue(__CFRunLoops, pthreadPointer(t));
    //如果还是获取不到RunLoop，就将当前线程t和刚创建的RunLoop都存储到全局字典__CFRunLoops中
	if (!loop) {
        //将当前线程t和刚创建的RunLoop都存储到全局字典__CFRunLoops中
	    CFDictionarySetValue(__CFRunLoops, pthreadPointer(t), newLoop);
        //赋值,用于返回值
	    loop = newLoop;
	}
        // don't release run loops inside the loopsLock, because CFRunLoopDeallocate may end up taking it
        __CFUnlock(&loopsLock);
    //release局部变量 newLoop
	CFRelease(newLoop);
    }
    //判断是否为当前线程，如果是就注册一个回调，当线程销毁时，顺便也销毁其对应的 RunLoop
    if (pthread_equal(t, pthread_self())) {
        _CFSetTSD(__CFTSDKeyRunLoop, (void *)loop, NULL);
        if (0 == _CFGetTSD(__CFTSDKeyRunLoopCntr)) {
            _CFSetTSD(__CFTSDKeyRunLoopCntr, (void *)(PTHREAD_DESTRUCTOR_ITERATIONS-1), (void (*)(void *))__CFFinalizeRunLoop);
        }
    }
    return loop;
}
```

由以上代码可知：

* 每条线程都有唯一的一个与之对应的RunLoop对象。
* RunLoop保存在一个全局的Dictionary里，线程作为key，RunLoop作为value。
* 线程刚创建时并没有RunLoop对象，RunLoop会在第一次获取该线程时创建与之对应的RunLoop对象。
* RunLoop会在线程结束时销毁。
* 主线程的RunLoop已经自动获取（创建），子线程默认没有开启RunLoop。

## RunLoop底层实现

Core Foundation中关于RunLoop的5个类：

* CFRunLoopRef
* CFRunLoopModeRef
* CFRunLoopSourceRef
* CFRunLoopTimerRef
* CFRunLoopObserverRef

RunLoop在源码中的定义：

```
struct __CFRunLoop {
    CFRuntimeBase _base;
    pthread_mutex_t _lock;			/* locked for accessing mode list */
    __CFPort _wakeUpPort;			// used for CFRunLoopWakeUp 
    Boolean _unused;
    volatile _per_run_data *_perRunData;              // reset for runs of the run loop
    pthread_t _pthread;//线程对象
    uint32_t _winthread;
    CFMutableSetRef _commonModes;
    CFMutableSetRef _commonModeItems;
    CFRunLoopModeRef _currentMode;
    CFMutableSetRef _modes;
    struct _block_item *_blocks_head;
    struct _block_item *_blocks_tail;
    CFAbsoluteTime _runTime;
    CFAbsoluteTime _sleepTime;
    CFTypeRef _counterpart;
};
```
我们将主要的成员变量挑选出来，简化一下：

```
struct __CFRunLoop {
    pthread_t _pthread;//线程对象
    CFMutableSetRef _commonModes;
    CFMutableSetRef _commonModeItems;
    CFRunLoopModeRef _currentMode;
    CFMutableSetRef _modes;
};
```

* CFMutableSetRef _modes：_modes是一个类似数组的集合，但是其所储存的数据类型是CFRunLoopModeRef,而且是无序的。
* CFRunLoopModeRef _currentMode：当前模式。

CFRunLoopModeRef的定义如下：

```
typedef struct __CFRunLoopMode *CFRunLoopModeRef;
```
那么__CFRunLoopMode又是怎么定义的呢？

```
struct __CFRunLoopMode {
	//部分成员变量
    CFStringRef _name;//mode名称
    CFMutableSetRef _sources0;
    CFMutableSetRef _sources1;
    CFMutableArrayRef _observers;
    CFMutableArrayRef _timers;
};
```

* CFMutableSetRef _sources0 和 CFMutableSetRef _sources1：这两个集合中存储的是 CFRunLoopSourceRef类型的数据。
* CFMutableArrayRef _observers：该集合中存储的是CFRunLoopObserverRef类型的数据。
* CFMutableArrayRef _timers：存储的是CFRunLoopTimerRef类型的数据。


![RunLoop相关的类之间的逻辑关系.png](https://upload-images.jianshu.io/upload_images/4164292-8aa39b9546acbadb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

综上所述可知，开头列出的5个类它们之间的关系是：CFRunLoopRef(__CFRunLoopMode)中存储着CFRunLoopModeRef类型的数据，CFRunLoopModeRef中存储着CFRunLoopSourceRef、 CFRunLoopTimerRef、CFRunLoopObserverRef 类型的数据。


## CFRunLoopModeRef

CFRunLoopModeRef代表RunLoop的运行模式。**一个RunLoop包含若干个Mode，每个Mode又包含若干个Source0/Source1/Timer/Observer。RunLoop启动时只能选择其中的一个Mode作为CurrentMode。如果需要切换Mode，只能退出当前Loop，再重新选择一个Mode进入，不同Mode中的Source0/Source1/Timer/Observer彼此分隔，互不影响。如果Mode里没有任何Source0/Source1/Timer/Observer，RunLoop会立马退出该Mode**。

### CFRunLoopModeRef 的 5 种 Mode

CFRunLoopModeRef 中一共有 5 种 Mode，mode 主要是用来指定事件在运行循环中的优先级的。这 5 种 mode 分别是：

* NSDefaultRunLoopMode(kCFRunLoopDefaultMode)：App 的默认 Mode，空闲状态，通常主线程是在这个 Mode 下运行。
* **UITrackingRunLoopMode**：**ScrollView 滑动时会切换到该 Mode**，这样可以保证界面滑动时不受其他 Mode 影响。
* **NSRunLoopCommonModes**(kCFRunLoopCommonModes)：通用模式，一旦设置此模式表示同时监听 NSDefaultRunLoopMode(kCFRunLoopDefaultMode) 和 UITrackingRunLoopMode 这两种模式。其实 kCFRunLoopMode 并不是一种真的模式，它只是一个标记。将定时器的 RunLoop 模式设置为kCFRunLoopCommonModes 意味着定时器(NSTimer)可以在“CFMutableSetRef __commonModes数组中存放的模式”下运行，而且 kCFRunLoopDefaultMode 和UITrackingRunLoopMode 都存放在CFMutableSetRef __commonModes数组中。
* UIInitializationRunLoopMode：在刚启动 App 时会切换到该 Mode，启动完成后就不再使用。
* GSEventReceiveRunLoopMode：用来接受系统事件的内部 Mode，通常用不到。


CFRunLoopModeRef 的 5 种 Mode 中最常见常用的模式是 kCFRunLoopDefaultMode(NSDefaultRunLoopMode)和 UITrackingRunLoopMode 这2种。

## CFRunLoopTimerRef

CFRunLoopTimerRef 是基于时间的触发器。基本上可以认为 CFRunLoopTimerRef 就是 NSTimer，它是受 RunLoop 的 Mode 影响。而 GCD 的定时器不受 RunLoop 的 Mode 影响。

## CFRunLoopSourceRef

CFRunLoopSourceRef 是**事件源（输入源）**。

按照**官方文档**，Source 可分为 3 类，包括：Custom Input Source、Cocoa Perform Selector Source 和 Port-Based Source。按照**函数调用栈**，Source 可分为 2 类，分别是 **Source0**（非基于 Port 的）和 **Source1**（基于 Port 的）。

* Source0:**负责 App 内部事件，由 App 负责管理触发**，包括：

&emsp;&emsp;(1)**触摸事件(UITouch 事件)处理**;

&emsp;&emsp;(2)**performSelector:onThread: 方法**

* Source1:Source1 是 CFRunLoopSourceRef 除了Source0 之外的另一个版本。Source1 除了包含回调指针外还包含一个**mach port**，和 Source0 需要手动触发不同，**Source1 可以监听系统端口和其他线程相互发送消息，它能够主动唤醒 RunLoop(由操作系统内核进行管理，例如 CFMessagePort 消息)**。

&emsp;&emsp;(1)**监听系统内核与其他线程间通信**;

&emsp;&emsp;(2)**接收、分发系统事件**



## CFRunLoopObserverRef

CFRunLoopObserverRef 是观察者，可以监听 RunLoop 的所有状态。RunLoop的所有状态包括：

```
/* Run Loop Observer Activities */
typedef CF_OPTIONS(CFOptionFlags, CFRunLoopActivity) {
    kCFRunLoopEntry = (1UL << 0),	//即将进入 RunLoop
    kCFRunLoopBeforeTimers = (1UL << 1),//即将处理 Timer
    kCFRunLoopBeforeSources = (1UL << 2),//即将处理 Source
    kCFRunLoopBeforeWaiting = (1UL << 5),//即将进入休眠
    kCFRunLoopAfterWaiting = (1UL << 6),//刚从休眠中唤醒
    kCFRunLoopExit = (1UL << 7),//即将退出 RunLoop
    kCFRunLoopAllActivities = 0x0FFFFFFFU
};
```

通过添加 Observer 监听 RunLoop 的所有状态，代码如下：

```
CFRunLoopObserverRef observer = CFRunLoopObserverCreateWithHandler(kCFAllocatorDefault, kCFRunLoopAllActivities, YES, 0, ^(CFRunLoopObserverRef observer, CFRunLoopActivity activity) {
        switch (activity) {
        case kCFRunLoopEntry:
            NSLog(@"kCFRunLoopEntry");
            break;
        case kCFRunLoopBeforeTimers:
            NSLog(@"kCFRunLoopBeforeTimers");
            break;
            
        case kCFRunLoopBeforeSources:
            NSLog(@"kCFRunLoopBeforeSources");
            break;
            
        case kCFRunLoopBeforeWaiting:
            NSLog(@"kCFRunLoopBeforeWaiting");
            break;
        case kCFRunLoopAfterWaiting:
            NSLog(@"kCFRunLoopAfterWaiting");
            break;
        case kCFRunLoopExit:
            NSLog(@"kCFRunLoopExit");
            break;
            
        default:
            break;
    }
    });
    //添加 Observer（观察者）到 RunLoop 中，以监听 RunLoop 的状态变化。
    CFRunLoopAddObserver(CFRunLoopGetMain(), observer, kCFRunLoopCommonModes);
    //释放observer
    CFRelease(observer);
```

## RunLoop 的运行逻辑

RunLoop的大体上的运行逻辑其实就是循环处理某种模式下的Source0、Source1、Timers、Observers。那么Source0、Source1、Timers、Observers具体表示什么呢？

* Source0:负责 App 内部事件，由 App 负责管理触发，包括：

&emsp;&emsp;(1)触摸事件(UITouch 事件)处理;

&emsp;&emsp;(2)performSelector:onThread: 方法

* Source1:Source1 是 CFRunLoopSourceRef 除了Source0 之外的另一个版本。Source1 除了包含回调指针外包含一个**mach port**，和 Source0 需要手动触发不同，**Source1 可以监听系统端口和其他线程相互发送消息，它能够主动唤醒RunLoop(由操作系统内核进行管理，例如 CFMessagePort 消息)**。

&emsp;&emsp;(1)**监听基于 Port 的线程间通信**;

&emsp;&emsp;(2)**接收、分发系统事件**

* Timers：

&emsp;&emsp;(1)NSTimers

&emsp;&emsp;(2)performSelector:WithObject:afterDelay:

* Observers:

&emsp;&emsp;(1)用于监听RunLoop的状态

&emsp;&emsp;(2)UI刷新（BeforeWaiting在休眠之前刷新UI）

&emsp;&emsp;(3)Autoreleasepool(BeforeWaiting)


RunLoop 具体的运行逻辑是这样的：

![RunLoop的运行逻辑.png](https://upload-images.jianshu.io/upload_images/4164292-d817bb1ea03226a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* (1)通知 Observers：即将进入 RunLoop；
* (2)通知 Observers：即将处理 Timers；
* (3)通知 Observers：即将处理 Sources；
* (4)处理 Blocks（比如：通过 CFRunLoopPerformBlock 函数添加的 Block）；
* (5)处理 Source0（可能会再次处理 Blocks）；
* (6)如果存在 Source1，就跳转到第 8 步，处理 Source1；
* (7)通知 Observers：开始休眠，不再占用CPU资源（等待消息唤醒）；
* (8)通知 Observers：结束休眠（被某个消息唤醒）。这个唤醒 RunLoop 的消息可能是 “Source0”（屏幕触摸事件或performSelector:onThread: 方法）、“RunLoop 设置的 timer 已经超时”或者是“RunLoop 被外部手动唤醒”等。


&emsp;&emsp;✅处理 Source1

&emsp;&emsp;✅处理 Timer（比如 timer 启动）

&emsp;&emsp;✅处理 GCD Async To Main Queue

* (9)处理Blocks；

* (10)根据前面的执行结果，决定如何操作：有可能不退出当前的RunLoop，而是回到第2步继续执行；也有可能结束当前 RunLoop，切换到其他的模式。如果切换到其他模式的话，则会执行(11)步；
* (11)通知Observers：退出RunLoop。


## RunLoop休眠的实现原理

![RunLoop休眠的实现原理.png](https://upload-images.jianshu.io/upload_images/4164292-0a83e4eb9f90a696.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


RunLoop休眠(线程阻塞)的实现原理：平时执行应用层面的代码时处于用户态，当在用户态调用mach_msg()函数时会自动转化到内核态,并调用内核态的mach_msg()函数，此时如果没有消息需要处理就让线程休眠，如果有消息需要处理就唤醒线程，回归到用户态调用应用层面的API去处理消息。


## RunLoop 在实际开发中的应用

* (1)控制线程生命周期（**线程保活**）

**线程保活的应用场景**：频繁执行一个任务或者多个串行（非并发）任务，可以采用线程保活的方式。线程保活的方式比传统的“创建线程-销毁线程-再创建线程-再销毁...”更节省CPU资源且更高效。比如 AFNetworking 中后台网络请求就使用了线程保活这种技术。

* (2)**解决 NSTimer 在滑动时停止工作(失效)的问题**

&emsp;&emsp;NSTimer 在滑动时失效的原因是 NSTimer 默认是工作在 NSDefaultRunLoopMode 模式下，而当我们滑动时，RunLoop 会退出NSDefaultRunLoopMode 模式，并进入 UITrackingRunLoopMode 模式，所有 NSTimer 失效。

【注意】[NSTimer scheduledTimerWithTimeInterval: repeats:block:]方法会自动将定时器添加到主线程的 NSDefaultRunLoopMode 模式下，如果要自定义 RunLoop 模式的话，可以改为使用 timerWithTimeInterval 方法创建定时器对象，并将定时器添加到当前线程的 NSRunLoopCommonModes 模式下(实际上是将timer 定时器添加到了 NSRunLoopCommonModes 模式下的 CFMutableSetRef _commonModeItems数组中)，这样就能解决timer失效的问题。代码如下：

```
NSTimer *timer = [NSTimer timerWithTimeInterval:self.completionDelay target:self selector:@selector(completionDelayTimerFired) userInfo:nil repeats:YES];

[[NSRunLoop currentRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];
```
 
* (3)**监控应用卡顿**
* (4)性能优化


## RunLoop总结

1.讲讲RunLoop，项目中用过吗？

* 控制线程生命周期（线程保活）
* 解决NSTimer在滑动时停止工作(失效)的问题

2.RunLoop内部实现逻辑是怎样的？

答：参照RunLoop具体的运行逻辑示意图

3.RunLoop和线程的关系是怎样的？

* 每条线程都有唯一的一个与之对应的RunLoop对象。
* RunLoop保存在一个全局的Dictionary里，线程作为key，RunLoop作为value。
* 线程刚创建时并没有RunLoop对象，RunLoop会在第一次获取该线程时创建与之对应的RunLoop对象。
* RunLoop会在线程结束时销毁。
* 主线程的RunLoop已经自动获取（创建），子线程默认没有开启RunLoop。

4.timer和RunLoop是怎样的关系？

* 从底层数据结构来看，RunLoop的__CFRunLoop结构体中存储着 CFMutableSetRef _modes,_modes是一个类似数组的集合，其所储存的数据类型是CFRunLoopModeRef, CFRunLoopModeRef结构体中存放着CFMutableArrayRef _timers。此外，如果timer被设置为 kCFRunLoopCommonModes模式，那么timer也将被存放在 __CFRunLoop结构体中的 CFMutableSetRef _commonModeItems数组中。
* 从RunLoop的运行逻辑来讲，timer会通知Observers结束休眠，唤醒线程来处理timer消息。

5.程序中添加每3秒响应一次的NSTimer，当拖动tableView或者scrollView时，timer可能无法响应要怎么解决？

NSTimer在滑动时失效的原因是NSTimer默认是工作在NSDefaultRunLoopMode(kCFRunLoopDefaultMode)模式下，而当我们滑动时，RunLoop会退出NSDefaultRunLoopMode模式，并进入UITrackingRunLoopMode模式(切换成UITrackingRunLoopMode模式是为了保证滑动的流畅)，导致NSTimer失效。

【注意】[NSTimer scheduledTimerWithTimeInterval: repeats:block:]方法会自动将定时器添加到主线程的NSDefaultRunLoopMode模式下，如果要自定义RunLoop模式的话，可以使用timerWithTimeInterval方法创建定时器对象，并将定时器添加到当前线程的**NSRunLoopCommonModes模式**下(实际上是将timer定时器添加到了NSRunLoopCommonModes 模式下的CFMutableSetRef _commonModeItems数组中)，这样就能解决timer失效的问题。代码如下：

```
NSTimer *timer = [NSTimer timerWithTimeInterval:self.completionDelay target:self selector:@selector(completionDelayTimerFired) userInfo:nil repeats:YES];

[[NSRunLoop currentRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];
```

6.RunLoop是怎么响应用户操作的，具体流程是什么样的？

当用户点击屏幕时，RunLoop内部的Source1会捕捉到该触屏事件，并将该事件包装成事件队列eventQueue交给Source0中进行处理。

7.说说RunLoop的几种状态

	kCFRunLoopEntry = (1UL << 0),   //即将进入RunLoop
    kCFRunLoopBeforeTimers = (1UL << 1),//即将处理Timer
    kCFRunLoopBeforeSources = (1UL << 2),//即将处理Source
    kCFRunLoopBeforeWaiting = (1UL << 5),//即将进入休眠
    kCFRunLoopAfterWaiting = (1UL << 6),//即将从休眠中唤醒
    kCFRunLoopExit = (1UL << 7),//即将推出RunLoop

8.RunLoop的mode作用是什么？

RunLoop常见的mode有2种：kCFRunLoopDefaultMode(NSDefaultRunLoopMode)和UITrackingRunLoopMode。kCFRunLoopDefaultMode是默认模式，通常主线程是在这个模式下运行；UITrackingRunLoopMode用于ScrollView追踪触摸滑动，保证界面滑动时不受其他mode影响。

mode的作用是将不同模式下的Source0/Source1/Timer/Observer隔离开来，互不影响，这样就提高了执行效率和滑动流畅性。
