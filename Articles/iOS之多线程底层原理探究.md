iOS之多线程底层原理探究

## iOS的几种多线程方案

![iOS中的常见多线程方案.png](https://upload-images.jianshu.io/upload_images/4164292-b43ac298722a0dd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## GCD

### GCD的常用函数

GCD([GCD源码](https://github.com/apple/swift-corelibs-libdispatch))中有2个用来执行任务的函数。分别是：

* 采用同步的方式执行任务

```
//queue代表队列，block代表任务
dispatch_sync(dispatch_queue_t queue, dispatch_block_t block);
```

具体使用如下：

```
//获取全局并发队列
    dispatch_queue_t queue = dispatch_get_global_queue(0, 0);
    dispatch_sync(queue, ^{
         NSLog(@"执行同步任务- %@",[NSThread currentThread]);
        //2019-06-30 16:04:15.286156+0800 GCD_001[26633:1513561] 执行同步任务- <NSThread: 0x6000027953c0>{number = 1, name = main}
    });
```

* 采用异步的方式执行任务

```
dispatch_async(dispatch_queue_t queue, dispatch_block_t block);
```

具体使用，代码如下：

```
//获取全局并发队列
    dispatch_queue_t queue = dispatch_get_global_queue(0, 0);
    dispatch_async(queue, ^{
        NSLog(@"执行异步任务- %@",[NSThread currentThread]);
        //2019-06-30 16:04:15.286429+0800 GCD_001[26633:1513619] 执行异步任务- <NSThread: 0x6000027f4000>{number = 3, name = (null)}
    });
```

### GCD的队列

GCD的队列可以分为两大类型，分别是

* **并发队列(Concurrent Dispatch Queue)**

并发队列是指可以让多个任务并发(同时)执行（自动开启多个线程同时执行任务）的队列。并发功能只有在异步(dispatch_async)函数下才有效。

* **串行队列(Serial Dispatch Queue)**

串行队列是指让任务一个接着一个地执行的队列（一个任务执行完毕后，再执行下一个任务）。

【注意】主队列(dispatch_queue queue = dispatch_get_main_queue();)其实也是串行队列。

### 同步/异步、并发/串行

(1)同步和异步主要影响的是：能不能开启新的线程。

* 同步(dispatch_sync)：在当前线程中执行任务，不具备开启新线程的能力。

* 异步(dispatch_async)：在新的线程中执行任务，具备开启新线程的能力。使用异步(dispatch_async)方式执行任务时只能说具备了开启新线程的能力，而并不一定开启新线程。比如传入的是主队列，那么就不会开启新的线程。

(2)并发和串行这两种队列类型主要影响的是：任务的执行方式。

* 并发：多个任务并发（同步）执行

* 串行：一个任务执行完毕后，再执行下一个任务。

### 各种队列的执行效果

![各种队列的执行效果.png](https://upload-images.jianshu.io/upload_images/4164292-e8467be7a9b24b18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

【注意】手动创建的串行队列指的是不包含主队列(也是串行队列)的串行队列。

由上表可知，只要是同步任务(sync)不管是哪种队列，都不会开启新线程，并且都是以串行方式执行任务；此外，只要是主队列（dispatch_get_main_queue();），不管是同步(sync)还是异步(async)都不会开启新线程，并且都是以串行方式执行任务。


## 死锁

所谓死锁，通常是指有两个线程T1和T2都卡住了，并且等待对方完成某些操作。T1不能完成是因为它在等待T2执行完成。而T2也不能完成，因为它在等待T1执行完成。也就是二者都处于“等待对方完成”的状态，于是就导致了死锁（DeadLock）。

以下代码会产生死锁吗？

```
- (void)viewDidLoad {
    [super viewDidLoad];
    NSLog(@"执行任务1");
    dispatch_queue_t queue = dispatch_get_main_queue();
    dispatch_sync(queue, ^{
        NSLog(@"执行任务2");
    });
    NSLog(@"执行任务3");

}
```

以上代码会产生死锁问题。

那么什么情况下会产生死锁呢？

是否产生死锁的判断标准：当使用**dispatch_sync**往**当前串行队列**中添加任务时，会卡住当前的串行队列，进而产生死锁。也就是同时满足下面这三个条件会产生死锁：

* (1)使用dispatch_sync函数
* (2)当前队列(同一个队列)
* (3)串行队列

【注意】只要同时满足以上3个条件就一定会产生死锁。所以解锁死锁，只需要打破以上3个条件中的任意一个，就可以解决死锁问题。


## 队列组的使用

队列组的使用场景：利用GCD实现以下功能——异步并发执行任务1、任务2，等任务1、任务2都执行完毕后，再回到主线程执行任务3。

代码如下：

```
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    //创建队列组
    dispatch_group_t group = dispatch_group_create();
    //创建并发队列
    dispatch_queue_t queue = dispatch_queue_create("my_queue", DISPATCH_QUEUE_CONCURRENT);
    //添加异步任务
    dispatch_group_async(group, queue, ^{
        for (int i = 0; i < 5; i++) {
            NSLog(@"任务1---%@",[NSThread currentThread]);
        }
    });
    dispatch_group_async(group, queue, ^{
        for (int i = 0; i < 5; i++) {
            NSLog(@"任务2---%@",[NSThread currentThread]);
        }
    });
    //等前面的任务执行完毕后，会自动执行这个任务
    //方法1
//    dispatch_group_notify(group, queue, ^{
//        dispatch_async(dispatch_get_main_queue(), ^{
//            for (int i = 0; i < 5; i++) {
//                NSLog(@"任务3---%@",[NSThread currentThread]);
//            }
//        });
//    });
    //方法2
    dispatch_group_notify(group, dispatch_get_main_queue(), ^{
        for (int i = 0; i < 5; i++) {
            NSLog(@"任务3---%@",[NSThread currentThread]);
        }
    });
    
}
```

## 多线程的安全隐患

* 资源共享。一块资源可能会被多个线程共享，也就是多个线程可能会访问同一块资源（比如多个线程访问同一个对象、同一个变量、同一个文件），很容易引发**数据错乱和数据安全问题**。

多线程安全隐患分析示意图：

![多线程安全隐患分析示意图.png](https://upload-images.jianshu.io/upload_images/4164292-ac3041ff3bb735cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

多线程安全问题的解决方案：使用**线程同步**技术（同步：协同步调，按预定的先后次序进行）。常用的线程同步技术是：**加锁**

![多线程安全隐患加锁示意图.png](https://upload-images.jianshu.io/upload_images/4164292-aa39e64672a572fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## iOS中的线程同步方案

* (1)OSSpinLock：等待锁的线程会处于忙等（busy-wait）状态，一直占用CPU资源，所以OSSpinLock是自旋锁(是一种高级锁)。目前OSSpinLock已不再安全，可能会出现优先级反转问题。有可能产生优先级反转的原因：如果等待锁的线程优先级比较高，它会一直占用着CPU资源，优先级低的线程就无法释放锁。所以在项目中不推荐使用OSSpinLock。

需要先导入头文件 #import <libkern/OSAtomic.h>，用法如下：

```
//初始化
OSSpinLock lock = OS_SPINLOCK_INIT;
//尝试加锁（如果需要等待就不加锁，直接返回false，如果不需要等待就加锁，返回true）
bool result = OSSpinLockTry(&lock);
//加锁
OSSpinLockLock(&lock);
//解锁
OSSpinLockUnlock(&_moneyLock);

```

* (2)os_unfair_lock：os_unfair_lock用于取代不安全的OSSpinLock，从iOS10开始才支持。从底层调用来看，等待os_unfair_lock锁的线程会处于休眠状态，并非忙等，所以os_unfair_lock是互斥锁，是一种low-level lock(低级锁：低级锁的特点是等不到锁的时候就进入休眠)。

使用时需要导入头文件 #import <os/lock.h>,具体用法如下：

```
//初始化
os_unfair_lock lock = OS_UNFAIR_LOCK_INIT;
//尝试加锁
os_unfair_lock_trylock(&lock);
//加锁
os_unfair_lock_lock(&lock);
//解锁
os_unfair_lock_unlock(&lock);

```

* (3)pthread_mutex：等待锁的线程会处于休眠状态，所以pthread_mutex是“互斥锁”，是一种low-level lock。

使用时需要导入头文件 #import <pthread.h>，具体用法如下：

首先声明一个属性锁：

```
@property (nonatomic, assign) pthread_mutex_t mutexLock;
```

```
//初始化属性
pthread_mutexattr_t attr;
pthread_mutexattr_init(&attr);
//PTHREAD_MUTEX_DEFAULT:普通锁
pthread_mutexattr_settype(&attr, PTHREAD_MUTEX_NORMAL);
//初始化锁
pthread_mutex_init(&_mutexLock,&attr);
//销毁锁的属性
pthread_mutexattr_destroy(&attr);
//加锁
pthread_mutex_lock(&_mutexLock);
//解锁
pthread_mutex_unlock(&_mutexLock);


- (void)dealloc
{
    //销毁锁
    pthread_mutex_destroy(&_mutexLock);
}
```

* (4)dispatch_semaphore：semaphore叫做“信号量”，信号量的初始值，可以用来控制线程并发访问的最大数量。信号量的初始值为1，表示同时只允许1条线程访问资源，保证线程同步。

```
//信号量的初始值
int value = 1;
//初始化信号量
dispatch_semaphore_t semaphore = dispatch_semaphore_create(value);
//如果信号量的值 > 0，就让信号量的值减1，然后继续往下执行后面的代码
//如果信号量的值 <=0，当前线程就会休眠等待，直到信号量的值变成 > 0，然后继续往下执行代码
dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
//让信号量的值加1
dispatch_semaphore_signal(semaphore);
```

* (5)dispatch_queue(DISPATCH_QUEUE_SERIAL)
* (6)NSLock：是对pthread_mutex普通锁的OC封装。
* (7)NSRecursiveLock：NSRecursiveLock也是对pthread_mutex递归锁的封装，API跟NSLock基本一致。
* (8)NSCondition：是对锁mutex和条件cond的OC封装。

```
@interface NSCondition : NSObject <NSLocking> {
@private
    void *_priv;
}

- (void)wait;
- (BOOL)waitUntilDate:(NSDate *)limit;
- (void)signal;
- (void)broadcast;
@end
```

* (9)NSConditionLock
* (10)@synchronized



## 多线程总结

* 1.简述你对多线程的理解

* 2.iOS的多线程方案有哪几种？你更倾向于哪一种？

* 3.用过GCD吗？在项目中具体是如何使用的？

* 4.GCD的队列类型有哪些？

* 5.说一下OperationQueue和GCD的区别以及各自的优势

* 6.线程安全的处理手段有哪些？

* 7.OC中的锁你了解哪些？使用以上这些锁需要注意哪些问题？自旋锁和互斥锁的异同？

* 8.任选C/OC/C++其中一种语言，实现自旋锁和互斥锁？

* 9.performSelector: withObject: afterDelay:方法的本质是往RunLoop中添加定时器，子线程默认没有启动RunLoop。


## GNUstep

GNUstep是GNU计划的项目之一，它将Cocoa的OC库重新实现了一遍并将其开源。虽然GNUstep不是苹果官方源码，但还是具有一定的参考价值。[GNUstep源码地址](http://www.gnustep.org/resources/downloads.php)

