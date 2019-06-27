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

iOS中有2套API来访问和使用RunLoop对象。其中一套基于OC的Foundation:NSRunLoop，另一套是基于C语言的Core Foundation:CFRunLoopRef。其中的NSRunLoop和CFRunLoopRef都代表着RunLoop对象。NSRunLoop是基于CFRunLoopRef的一层OC包装(这也是使用OC方式和C语言方式打印出来的主线程不同的原因，主线程真正的地址是使用C语言打印出来的地址)。CFRunLoopRef是开源的。[CFRunLoopRef下载链接](https://opensource.apple.com/tarballs/CF/)

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

综上所述可知，开头列出的5个类它们之间的关系是：CFRunLoopRef(__CFRunLoopMode)中存储着CFRunLoopModeRef类型的数据，CFRunLoopModeRef中存储着CFRunLoopSourceRef、 CFRunLoopTimerRef、CFRunLoopObserverRef类型的数据。


## CFRunLoopModeRef

CFRunLoopModeRef代表RunLoop的运行模式。一个RunLoop包含若干个Mode，每个Mode又包含若干个Source0/Source1/Timer/Observer。RunLoop启动时只能选择其中的一个Mode作为CurrentMode。如果需要切换Mode，只能退出当前Loop，再重新选择一个Mode进入，不同Mode中的Source0/Source1/Timer/Observer彼此分隔，互不影响。如果Mode里没有任何Source0/Source1/Timer/Observer，RunLoop会立马退出该Mode。

### CFRunLoopModeRef的5种Mode

CFRunLoopModeRef中一共有5种Mode，分别是：

* kCFRunLoopDefaultMode(NSDefaultRunLoopMode)：App的默认Mode，通常主线程是在这个Mode下运行。
* UITrackingRunLoopMode：界面跟踪Mode，用于ScrollView追踪触摸滑动，保证界面滑动时不受其他Mode影响。
* UIInitializationRunLoopMode：在刚启动 App 时进入的第一个 Mode，启动完成后就不再使用。
* GSEventReceiveRunLoopMode：用来接受系统事件的内部 Mode，通常用不到。
* kCFRunLoopCommonModes：通用模式，一旦设置此模式表示同时监听kCFRunLoopDefaultMode和UITrackingRunLoopMode这两种模式。

CFRunLoopModeRef的5种Mode中最常见常用的模式是kCFRunLoopDefaultMode(NSDefaultRunLoopMode)和UITrackingRunLoopMode这2种。

## RunLoop的运行逻辑

RunLoop的运行逻辑其实就是循环处理某种模式下的Source0、Source1、Timers、Observers。

* Source0:

&emsp;&emsp;(1)触摸事件处理;

&emsp;&emsp;(2)performSelector:onThread:方法

* Source1:

&emsp;&emsp;(1)基于Port的线程间通信;

&emsp;&emsp;(2)系统事件捕捉

* Timers：

&emsp;&emsp;(1)NSTimers

&emsp;&emsp;(2)performSelector:WithObject:afterDelay:

* Observers:

&emsp;&emsp;(1)用于监听RunLoop的状态

&emsp;&emsp;(2)UI刷新（BeforeWaiting在休眠之前刷新UI）

&emsp;&emsp;(3)Autoreleasepool

## RunLoop总结

1.讲讲RunLoop，项目中用过吗？

2.RunLoop内部实现逻辑是怎样的？

3.RunLoop和线程的关系是怎样的？

4.timer和RunLoop是怎样的关系？

5.程序中添加每3秒响应一次的NSTimer，当拖动tableView时，timer可能无法响应要怎么解决？

6.RunLoop是怎么响应用户操作的，具体流程是什么样的？

7.说说RunLoop的几种状态

8.RunLoop的mode作用是什么？