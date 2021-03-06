

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

## iOS中的10种线程同步方案

* (1)OSSpinLock(自旋锁)：等待锁的线程会处于忙等（busy-wait）状态，一直占用CPU资源，所以**OSSpinLock是自旋锁**(是一种高级锁)。目前OSSpinLock已不再安全，可能会出现优先级反转(Priority Inversion)的问题，所以在项目中不推荐使用OSSpinLock。那么OSSpinLock为什么会产生优先级反转呢？主要原因发生在低优先级线程拿到锁时，高优先级线程进入忙等(busy-wait)状态，消耗大量 CPU 时间，从而导致低优先级线程拿不到 CPU 时间，也就无法完成任务并释放锁。这种问题被称为优先级反转。

为什么高优先级线程处于“忙等”状态会导致低优先级线程拿不到时间片？这还得从操作系统的线程调度说起。现代操作系统在管理普通线程时，通常采用时间片轮转算法(Round Robin，简称 RR)。每个线程会被分配一段时间片(quantum)，通常在 10-100 毫秒左右。当线程用完属于自己的时间片以后，就会被操作系统挂起，放入等待队列中，直到下一次被分配时间片。

**OSSpinLock自旋锁的使用**

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

**[自旋锁实现原理](https://juejin.im/post/57f6e9f85bbb50005b126e5f)**

自旋锁的目的是为了确保临界区只有一个线程可以访问，它的使用可以用下面这段伪代码来描述:

```
do {
    Acquire Lock
        Critical section  // 临界区
    Release Lock
        Reminder section // 不需要锁保护的代码
}
```

在 Acquire Lock 这一步，我们申请加锁，目的是为了保护临界区(Critical Section) 中的代码不会被多个线程执行。

自旋锁的实现思路很简单，理论上来说只要定义一个全局变量，用来表示锁的可用情况即可，伪代码如下:

```
bool lock = false; // 一开始没有锁上，任何线程都可以申请锁
do {
    while(lock); // 如果 lock 为 true 就一直死循环，相当于申请锁
    lock = true; // 挂上锁，这样别的线程就无法获得锁
        Critical section  // 临界区
    lock = false; // 相当于释放锁，这样别的线程可以进入临界区
        Reminder section // 不需要锁保护的代码        
}
```

这段代码存在一个问题: 如果一开始有多个线程同时执行 while 循环，他们都不会在这里卡住，而是继续执行，这样就无法保证锁的可靠性了。解决思路也很简单，只要确保申请锁的过程是原子操作即可。

**原子操作**

狭义上的原子操作表示一条不可打断的操作，也就是说线程在执行操作过程中，不会被操作系统挂起，而是一定会执行完。在单处理器环境下，一条汇编指令显然是原子操作，因为中断也要通过指令来实现。

然而在多处理器的情况下，能够被多个处理器同时执行的操作任然算不上原子操作。因此，真正的原子操作必须由硬件提供支持，比如 x86 平台上如果在指令前面加上 “LOCK” 前缀，对应的机器码在执行时会把总线锁住，使得其他 CPU不能再执行相同操作，从而从硬件层面确保了操作的原子性。

上述申请锁的过程，可以用一个原子性操作 test_and_set 来完成，它用伪代码可以这样表示:

```
bool test_and_set (bool *target) {
    bool rv = *target; 
    *target = TRUE; 
    return rv;
}
```

这段代码的作用是把 target 的值设置为 1，并返回原来的值。当然，在具体实现时，它通过一个原子性的指令来完成。

**自旋锁实现的完整伪代码**如下：

```
bool lock = false; // 一开始没有锁上，任何线程都可以申请锁
do {
    while(test_and_set(&lock); // test_and_set 是一个原子操作
        Critical section  // 临界区
    lock = false; // 相当于释放锁，这样别的线程可以进入临界区
        Reminder section // 不需要锁保护的代码        
}
```
**自旋锁使用注意事项**：

* <1>**过多占用CPU资源**

如果临界区的执行时间过长，不适合使用自旋锁(原因：过多占用CPU资源)**。之前我们介绍过时间片轮转算法，线程在多种情况下会退出自己的时间片。其中一种是用完了时间片的时间，被操作系统强制抢占。除此以外，当线程进行 I/O 操作，或进入睡眠状态时，都会主动让出时间片。显然在 while 循环中，线程处于忙等状态，白白浪费 CPU 时间，最终因为超时被操作系统抢占时间片。如果临界区执行时间较长，比如是文件读写，这种忙等是毫无必要的。

* <2>**引起死锁**

自旋锁使用不当可能引起**死锁**问题。**试图递归地获得自旋锁必然会引起死锁。**所以递归程序决不能在持有自旋锁时调用它自己，也决不能在递归调用时试图获得相同的自旋锁。


* (2)os_unfair_lock(互斥锁)：os_unfair_lock用于取代不安全的OSSpinLock，从iOS10开始才支持。从底层调用来看，等待os_unfair_lock锁的线程会处于休眠状态，而不是像自旋锁那样忙等，所以os_unfair_lock是互斥锁，是一种low-level lock(低级锁：低级锁的特点是等不到锁的时候就进入休眠)。

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

* (3)pthread_mutex(互斥锁)：等待锁的线程会处于休眠状态，所以pthread_mutex是“互斥锁”，是一种low-level lock。互斥锁的实现原理与信号量非常相似，不是使用忙等，而是阻塞线程并休眠，需要进行上下文切换。

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
pthread_mutexattr_settype(&attr, PTHREAD_MUTEX_NORMAL);//定义锁的属性
//初始化锁
pthread_mutex_init(&_mutexLock,&attr);
//销毁锁的属性
pthread_mutexattr_destroy(&attr);
//加锁
pthread_mutex_lock(&_mutexLock);
//临界区代码
//解锁
pthread_mutex_unlock(&_mutexLock);


- (void)dealloc
{
    //销毁锁
    pthread_mutex_destroy(&_mutexLock);
}
```

由于 pthread_mutex 有多种类型，有 PTHREAD_MUTEX_NORMAL、PTHREAD_MUTEX_ERRORCHECK、PTHREAD_MUTEX_RECURSIVE、PTHREAD_MUTEX_DEFAULT等。

如果互斥锁类型为 PTHREAD_MUTEX_NORMAL，则不提供死锁检测。尝试重新锁定互斥锁会导致死锁。如果某个线程尝试解除锁定的互斥锁不是由该线程锁定或未锁定，则将产生不确定的行为。

如果互斥锁类型为 PTHREAD_MUTEX_ERRORCHECK，则会提供错误检查。如果某个线程尝试重新锁定的互斥锁已经由该线程锁定，则将返回错误。如果某个线程尝试解除锁定的互斥锁不是由该线程锁定或者未锁定，则将返回错误。

如果互斥锁类型为 PTHREAD_MUTEX_RECURSIVE，则该互斥锁会保留锁定计数这一概念。线程首次成功获取互斥锁时，锁定计数会设置为 1。线程每重新锁定该互斥锁一次，锁定计数就增加 1。线程每解除锁定该互斥锁一次，锁定计数就减小 1。 锁定计数达到 0 时，该互斥锁即可供其他线程获取。如果某个线程尝试解除锁定的互斥锁不是由该线程锁定或者未锁定，则将返回错误。

如果互斥锁类型是 PTHREAD_MUTEX_DEFAULT，则尝试以递归方式锁定该互斥锁将产生不确定的行为。对于不是由调用线程锁定的互斥锁，如果尝试解除对它的锁定，则会产生不确定的行为。如果尝试解除锁定尚未锁定的互斥锁，则会产生不确定的行为。

**pthread_mutex使用注意事项：**

一般情况下，一个线程只能申请一次锁，也只能在获得锁的情况下才能释放锁，多次申请锁或释放未获得的锁都会导致崩溃。假设在已经获得锁的情况下再次申请锁，线程会因为等待锁的释放而进入睡眠状态，因此就不可能再释放锁，从而导致死锁。然而这种情况经常会发生，比如某个函数申请了锁，在临界区内又递归调用了自己。幸运的是 pthread_mutex 支持递归锁，也就是允许一个线程递归的申请锁，只要把 attr 的类型改成 PTHREAD_MUTEX_RECURSIVE 即可(递归锁的特点：允许同一条线程对同一把锁进行重复加锁而不会引发死锁)。

由于 pthread_mutex 有多种类型，可以支持递归锁等，因此在申请加锁时，需要对锁的类型加以判断，这也就是为什么它和信号量的实现类似，但效率略低的原因。

**pthread_mutex-递归锁**

将pthread_mutexattr_settype(&attr, PTHREAD_MUTEX_RECURSIVE);的第二个参数设置为PTHREAD_MUTEX_RECURSIVE，表示该锁是**递归锁**。除此属性值不同之外，其他用法与pthread_mutex默认的普通锁(PTHREAD_MUTEX_NORMAL)完全一致。

递归锁的特点：允许同一条线程对同一把锁进行重复加锁而不会引发死锁。因为只是允许同一条线程重复加锁，所以递归锁依然能够保证线程安全。


**pthread_mutex-条件锁**

具体用法参考下面的“多线程的线程间依赖”。

* (4)dispatch_semaphore：semaphore叫做“信号量”，信号量的初始值，可以用来控制线程并发访问的最大数量。**信号量的初始值为1，表示同时只允许1条线程访问资源，保证线程同步(实现线程同步的一种方法)**。

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
dispatch_semaphore实现原理大致如下：Dispatch Semaphore信号量主要是dispatch_semaphore_wait和dispatch_semaphore_signal函数，wait会将信号量值减一，如果大于等于0就立即返回，否则等待信号量唤醒或者超时；signal会将信号量值加一，如果value大于0立即返回，否则唤醒某个等待中的线程。

**[dispatch_semaphore实现原理详解](https://xiaozhuanlan.com/topic/4365017982)**

**dispatch_semaphore注意事项：**

信号量在销毁或重新创建的时候如果还在使用则会引起崩溃。

* (5)dispatch_queue(DISPATCH_QUEUE_SERIAL)：直接使用GCD的串行队列，也是可以实现线程同步的。

```
#import "Dispatch_Queue_SerialDemo.h"
@interface Dispatch_Queue_SerialDemo ()
@property (nonatomic, strong) dispatch_queue_t ticketQueue;
@property (nonatomic, strong) dispatch_queue_t moneyQueue;
@end

@implementation Dispatch_Queue_SerialDemo
- (instancetype)init
{
    if (self = [super init]) {
        _ticketQueue = dispatch_queue_create("ticketQueue", DISPATCH_QUEUE_SERIAL);
        _moneyQueue = dispatch_queue_create("moneyQueue", DISPATCH_QUEUE_SERIAL);
    }
    return self;
}
- (void)hl_saveMoney
{
    dispatch_sync(_moneyQueue, ^{
        [super hl_saveMoney];
    });
    
}
- (void)hl_drawMoney
{
    dispatch_sync(_moneyQueue, ^{
        
        [super hl_drawMoney];
    });
}
- (void)hl_saleTicket
{
    dispatch_sync(_ticketQueue, ^{
        [super hl_saleTicket];
    });
    
}
@end
```

* (6)NSLock：是对pthread_mutex普通锁的OC形式的封装。NSLock 只是在内部封装了一个 pthread_mutex，属性为 PTHREAD_MUTEX_ERRORCHECK，它会损失一定性能换来错误提示。

NSLock的实现非常简单，通过宏，定义了 lock 方法。这里使用宏定义的原因是，OC 内部还有其他几种锁，他们的 lock 方法都是一模一样，仅仅是内部 pthread_mutex 互斥锁的类型不同。通过宏定义，可以简化方法的定义。

```
#import "NSLockDemo.h"
@interface NSLockDemo ()
@property(nonatomic, strong)NSLock *ticketLock;
@property(nonatomic, strong)NSLock *moneyLock;
@end

@implementation NSLockDemo
- (instancetype)init
{
    if (self = [super init]) {
        self.ticketLock = [[NSLock alloc] init];
        self.moneyLock = [[NSLock alloc] init];
    }
    return self;
}
- (void)hl_saveMoney
{
    [self.moneyLock lock];
    [super hl_saveMoney];
    [self.moneyLock unlock];
}
- (void)hl_drawMoney
{
    [self.moneyLock lock];
    [super hl_drawMoney];
    [self.moneyLock unlock];
}
- (void)hl_saleTicket
{
    [self.moneyLock lock];
    [super hl_saleTicket];
    [self.moneyLock unlock];
}
@end
```
* (7)NSRecursiveLock(递归锁)：NSRecursiveLock也是对**pthread_mutex递归锁**OC形式的封装，API跟NSLock基本一致。

NSRecursiveLock 与 NSLock 的区别在于内部封装的 pthread_mutex_t 对象的类型不同，NSRecursiveLock的类型为 PTHREAD_MUTEX_RECURSIVE，而NSLock的类型为 PTHREAD_MUTEX_ERRORCHECK。

* (8)NSCondition(条件锁)：NSCondition条件锁，是对锁mutex和条件cond的OC封装。

NSCondition 的底层是通过条件变量(condition variable) pthread_cond_t和互斥锁来实现的。条件变量有点像信号量，提供了线程阻塞与信号机制，因此可以用来阻塞某个线程，并等待某个数据就绪，随后唤醒线程，比如常见的生产者-消费者模式。

NSCondition的应用场景是某条线程，比如线程A执行到某处时发现条件不满足，此时就会在此处等待，并打开这把锁，允许其他线程去执行任务（加锁解锁）。当其他线程执行完任务后，会解锁，并发送一个符合条件的信号，此时条件满足了，线程A就会给这把锁重新加锁，并往下继续执行代码。NSConditionLock主要用在按照一定顺序执行任务的场合。

```
@interface NSCondition : NSObject <NSLocking> {
@private
    void *_priv;
}
//等待
- (void)wait;
- (BOOL)waitUntilDate:(NSDate *)limit;
//唤醒一条正在等待的线程
- (void)signal;
//广播，唤起所有等待的线程
- (void)broadcast;
@end
```

NSCondition遵守了NSLocking协议，使用的时候同样是lock，unlock加解锁。

具体用法，代码如下：

```
#import "NSConditionDemo.h"

@interface NSConditionDemo ()
@property (nonatomic, strong) NSCondition *condition;//条件
@property (nonatomic, strong) NSMutableArray *dataArr;
@end
@implementation NSConditionDemo

- (instancetype)init
{
    if (self = [super init]) {
        self.condition = [[NSCondition alloc] init];
        self.dataArr = [NSMutableArray array];
    }
    return self;
}
- (void)otherTest
{
    [[[NSThread alloc] initWithTarget:self selector:@selector(hl_remove) object:nil] start];
    [[[NSThread alloc] initWithTarget:self selector:@selector(hl_add) object:nil] start];
}
//线程1
- (void)hl_remove
{
    [self.condition lock];
    NSLog(@"hl_remove - begin");
    if (self.dataArr.count == 0) {
        //等待
        [self.condition wait];
    }
    [self.dataArr removeAllObjects];
    NSLog(@"删除了元素");
    [self.condition unlock];
}
//线程2
- (void)hl_add
{
     [self.condition lock];
    sleep(1);
    [self.dataArr addObject:@"Test"];
    NSLog(@"添加了元素");
    //信号：唤醒一个等待该条件的线程
    [self.condition signal];
//    //广播
//    [self.condition broadcast];
    [self.condition unlock];
}
```

* (9)NSConditionLock(条件锁)：是对NSCondition的进一步封装，可以设置具体的条件值。它的本质就是一个生产者-消费者模型。
API如下：

```
@interface NSConditionLock : NSObject <NSLocking> {
@private
    void *_priv;
}

- (instancetype)initWithCondition:(NSInteger)condition NS_DESIGNATED_INITIALIZER;

@property (readonly) NSInteger condition;
//消费者方法
- (void)lockWhenCondition:(NSInteger)condition;
- (BOOL)tryLock;
- (BOOL)tryLockWhenCondition:(NSInteger)condition;
//生产者方法
- (void)unlockWithCondition:(NSInteger)condition;
- (BOOL)lockBeforeDate:(NSDate *)limit;
- (BOOL)lockWhenCondition:(NSInteger)condition beforeDate:(NSDate *)limit;
@end
```

具体用法如下：

```
@interface NSConditionLockDemo ()
@property (nonatomic, strong) NSConditionLock *conditionLock;//条件
@end
@implementation NSConditionLockDemo

- (instancetype)init
{
    if (self = [super init]) {
        //将条件锁内部的条件设置为1.
        self.conditionLock = [[NSConditionLock alloc] initWithCondition:1];
    }
    return self;
}
- (void)otherTest
{
    [[[NSThread alloc] initWithTarget:self selector:@selector(hl_threadA) object:nil] start];
    [[[NSThread alloc] initWithTarget:self selector:@selector(hl_threadB) object:nil] start];
}
//线程B
- (void)hl_threadB
{
    //当这把锁内存所存储的条件值为1时，才能加锁，否则，就等待
    [self.conditionLock lockWhenCondition:1];
    NSLog(@"hl_threadB");
    [self.conditionLock unlockWithCondition:2];
}
//线程A
- (void)hl_threadA
{
    [self.conditionLock lockWhenCondition:2];
    NSLog(@"hl_threadA");
    [self.conditionLock unlockWithCondition:2];
}
```

* (10)@synchronized：是对**pthread_mutex递归锁**的封装，支持递归加锁。其实现源码可以在objc4的objc-sync.mm文件查看。

@synchronized的实现原理是：利用HashMap（哈希表/散列表），将传入的对象作为key，并通过key获取一把与之对应的锁(Value)；如果传入的对象相同，那么获取的锁也是相同的；如果传入的对象不同，那么得到的是不同的锁。

```
#import "SynchronizedDemo.h"
@implementation SynchronizedDemo
//@synchronized在实现线程同步的各种方案中用法最简单，但是@synchronized的性能消耗大。
//存钱取钱共用一把锁，卖票使用另一把锁，这样做是为了可以让存钱取钱操作和卖票操作异步执行。
- (void)hl_saveMoney
{
    @synchronized([self class]) {
        [super hl_saveMoney];
    }
}
- (void)hl_drawMoney
{
    @synchronized ([self class]) {
        
        [super hl_drawMoney];
    }
}
- (void)hl_saleTicket
{
    static NSObject *lock;
    static dispatch_once_t onceTocken;
    dispatch_once(&onceTocken, ^{
        lock = [[NSObject alloc]init];
    });
    @synchronized (lock) {
        [super hl_saleTicket];
    }
}
@end
```

## 多线程的线程间依赖

多线程的线程间依赖指的是有时由于业务需要，只有等执行完线程B中的任务才能再执行线程A中的任务，也就是要求线程A和线程B的执行顺序有要求。那么如何实现线程间依赖呢？可以通过"pthread_mutex-条件锁"来实现。

代码如下：

```
#import "Pthread_mutexDemo3.h"
#import <pthread.h>
@interface Pthread_mutexDemo3 ()
@property (nonatomic, assign) pthread_mutex_t mutex;
@property (nonatomic, assign) pthread_cond_t cond;//条件
@property (nonatomic, strong) NSMutableArray *dataArr;
@end

@implementation Pthread_mutexDemo3
- (instancetype)init
{
    if (self = [super init]) {
        //初始化属性
        pthread_mutexattr_t attr;
        pthread_mutexattr_init(&attr);
        pthread_mutexattr_settype(&attr, PTHREAD_MUTEX_RECURSIVE);
        //初始化锁
        pthread_mutex_init(&_mutex, &attr);
        //销毁属性
        pthread_mutexattr_destroy(&attr);
        //初始化条件
        pthread_cond_init(&_cond, NULL);
        self.dataArr = [NSMutableArray array];
    }
    return self;
}
- (void)otherTest
{
    [[[NSThread alloc] initWithTarget:self selector:@selector(hl_remove) object:nil] start];
    [[[NSThread alloc] initWithTarget:self selector:@selector(hl_add) object:nil] start];
}
//线程1

- (void)hl_remove
{
    pthread_mutex_lock(&_mutex);
    //wait 方法除了会被 signal 方法唤醒，有时还会被虚假唤醒，所以需要在这里进行判断。
    if (self.dataArr.count == 0) {
        //等待(休眠)条件_cond，将锁_mutex放开，被唤醒之后，重新加锁_mutex
        //_cond:将来用来唤醒自己的条件。注意等待的条件必须和唤醒的条件完全一样。
        pthread_cond_wait(&_cond, &_mutex);
    }
    [self.dataArr removeAllObjects];
    pthread_mutex_unlock(&_mutex);
}
//线程2
- (void)hl_add
{
    //添加互斥锁以保证消费者拿到的数据是线程安全的。
    pthread_mutex_lock(&_mutex);
    [self.dataArr addObject:@"Test"];
    //信号：唤醒一个等待该条件的线程
    pthread_cond_signal(&_cond);
//    //广播:唤醒所有等待该条件的线程
//    pthread_cond_broadcast(&_cond);
    pthread_mutex_unlock(&_mutex);
}
- (void)dealloc
{
    pthread_mutex_destroy(&_mutex);
    pthread_cond_destroy(&_cond);
}
@end
```

## GCD的dispatch_semaphore(信号量)实现“控制最大并发量”

代码如下：

```
#import "SemaphoreDemo.h"

@interface SemaphoreDemo ()
@property (nonatomic, strong) dispatch_semaphore_t semaphore;
@end

@implementation SemaphoreDemo
- (instancetype)init
{
    if (self = [super init]) {
        //初始化信号量
        self.semaphore = dispatch_semaphore_create(5);
    }
    return self;
}
- (void)otherTest
{
    for (int i = 0; i < 20; i++) {
        [[[NSThread alloc]initWithTarget:self selector:@selector(test) object:nil] start];
    }
}
- (void)test
{
    //如果信号量的值 > 0，就让信号量的值减1，然后继续往下执行代码
    //如果信号量的值 <=0，就会休眠等待。直到信号量的值变成 > 0，然后继续往下执行代码
    dispatch_semaphore_wait(self.semaphore, DISPATCH_TIME_FOREVER);//DISPATCH_TIME_FOREVER永远等待
    sleep(2);
    NSLog(@"test- %@",[NSThread currentThread]);
    //dispatch_semaphore_signal作用：使信号量的值加1
    dispatch_semaphore_signal(self.semaphore);

}
@end
```

## GCD的dispatch_semaphore(信号量)实现“线程同步”

信号量的初始值为1(也就是dispatch_semaphore_create(1))，表示同时只允许1条线程访问资源，可以实现线程同步(实现线程同步的一种方法)

代码如下：

```
#import "SemaphoreDemo2.h"
@interface SemaphoreDemo2 ()
@property(nonatomic, strong) dispatch_semaphore_t ticketSemaphore;
@property(nonatomic, strong) dispatch_semaphore_t moneySemaphore;
@end

@implementation SemaphoreDemo2
- (instancetype)init
{
    if (self = [super init]) {
        //初始化信号量
        self.ticketSemaphore = dispatch_semaphore_create(1);
        self.moneySemaphore = dispatch_semaphore_create(1);
    }
    return self;
}
- (void)hl_saveMoney
{
    dispatch_semaphore_wait(self.moneySemaphore, DISPATCH_TIME_FOREVER);
    [super hl_saveMoney];
    dispatch_semaphore_signal(self.moneySemaphore);
}
- (void)hl_drawMoney
{
    dispatch_semaphore_wait(self.moneySemaphore, DISPATCH_TIME_FOREVER);
    [super hl_drawMoney];
    dispatch_semaphore_signal(self.moneySemaphore);
}
- (void)hl_saleTicket
{
    dispatch_semaphore_wait(self.ticketSemaphore, DISPATCH_TIME_FOREVER);
    [super hl_saleTicket];
    dispatch_semaphore_signal(self.ticketSemaphore);
    
}

@end
```

## iOS线程同步方案性能比较

iOS的各种线程同步方案的实际性能在不同系统，不同设备上可能会有些许差异。ibireme曾对各种锁进行了性能测试([性能测试代码链接](https://github.com/ibireme/tmp))，大家可以参考。

性能从高到低排序：

* os_unfair_lock(从iOS10开始支持)
* OSSpinLock(不建议使用)
* dispatch_semaphore(推荐使用)
* pthread_mutex(推荐使用)
* dispatch_queue(DISPATCH_QUEUE_SERIAL)
* NSLock
* NSCondition
* pthread_mutex(recursive)
* NSRecursiveLock
* NSConditionLock
* @synchronized

## 自旋锁、互斥锁比较

1.什么情况下使用自旋锁比较划算？

* 预计线程等待锁的时间很短。
* 加锁的代码（临界区）经常被调用，但竞争情况很少发生。
* CPU资源不紧张
* 多核处理器

2.什么情况下使用互斥锁比较划算？

* 预计线程等待锁的时间较长
* 单核处理器
* 临界区有IO操作（因为IO操作都占用CPU资源比较大）
* 临界区代码复杂或者循环量大
* 临界区竞争非常激烈

## atomic

nonatomic:非原子性

atomic:原子性。给属性加上atomic修饰，可以保证属性的setter和getter方法都是原子性操作，相当于在getter和setter内部加上了线程同步的锁。但是**atomic并不能保证使用属性的过程是线程安全的**。iOS实际开发中之所以不适用atomic修饰属性是因为atomic非常消耗CPU资源。

atomic源码实现可以查看objc4的**objc-accessors.mm**


## iOS中的读写安全最佳方案

iOS中的读写，指的是IO操作（文件操作），包括从文件中读取内容和往文件中写入内容。

```
- (void)viewDidLoad {
    [super viewDidLoad];
    for (int i = 0; i < 5; i++) {
        [[[NSThread alloc] initWithTarget:self selector:@selector(read) object:nil] start];
        [[[NSThread alloc] initWithTarget:self selector:@selector(write) object:nil] start];
    }
}
- (void)read
{
    //缺点：读操作也是串行，效率低。
    //优化：允许多条线程同时读取数据。实现“多读单写”
    //多读单写：同一时间，只能有1条线程进行写的操作；同一时间，允许有多个线程进行读的操作；同一时间，不允许既有写的操作，又有读的操作。
    dispatch_semaphore_wait(self.semaphore, DISPATCH_TIME_FOREVER);
    NSLog(@"%s",__func__);
    dispatch_semaphore_signal(self.semaphore);
}
- (void)write
{
    dispatch_semaphore_wait(self.semaphore, DISPATCH_TIME_FOREVER);
    NSLog(@"write ------%s",__func__);
    dispatch_semaphore_signal(self.semaphore);
}
```
以上读写操作加锁后虽然是安全的，但是同一时间只能一条线程读取或写入，并不能达到“多读单写”的目标，因此并不是最优解。最优解必须满足“多读单写”的要求：

* (1)同一时间，只能有1条线程进行写的操作；
* (2)同一时间，允许有多个线程进行读的操作；
* (3)同一时间，不允许既有写的操作，又有读的操作。

那么iOS中实现“多读单写”的方案有哪些呢？

* (1) **pthread_rwlock:读写锁**：等待锁的线程会进入休眠状态。

```
pthread_rwlock_t lock;
//初始化锁
pthread_rwlock_init(&_lock, NULL);
//读操作-加锁
pthread_rwlock_rdlock(&_lock);
//多操作-尝试加锁
pthread_rwlock_tryrdlock(&_lock);
//写操作-加锁
pthread_rwlock_wrlock(&_lock);
//写操作-尝试加锁
pthread_rwlock_trywrlock(&_lock);
//解锁
pthread_rwlock_unlock(&_lock);
//销毁
pthread_rwlock_destroy(&_lock);
```

* (2) **dispatch_barrier_async:异步栅栏调用**：

dispatch_barrier_async函数传入的**并发队列必须是自己通过dispatch_queue_create创建的**,读操作和写操作要使用同一个并发队列。如果dispatch_barrier_async函数传入的是一个串行队列或者一个全局的并发队列，那这个函数便等同于dispatch_async函数的效果。

具体使用方法：

```
//初始化并发队列
dispatch_queue_t queue = dispatch_queue_create("rw_queue", DISPATCH_QUEUE_CONCURRENT);
//读操作
dispatch_async(_queue, ^{

});
//写操作
dispatch_barrier_sync(_queue, ^{

});
```

综上所述，使用pthread_rwlock(读写锁)和dispatch_barrier_async(异步栅栏调用)这两种方案都可以解决读写安全问题，实现“多读单写”的功能。

## 多线程总结

 **1.简述你对多线程的理解**

思路：多线程的概念和作用；多线程的优势。

谈多线程之前，我们先来了解以下进程的概念。进程是指在系统中正在运行的一个应用程序(同时打开QQ和Xcode,系统会分别启动2个进程)，其中，每个进程之间是独立的,每个进程均运行在其专用的且受保护的内存空间内。那线程是什么呢？线程是进程的基本执行单元,进程的所有任务都在线程中执行。一个进程想要执行任务,必须得有线程(每个进程至少要有一条线程,即主线程)。那什么是多线程呢？一个进程中可以开启多条线程,每条线程可以并行(同时)执行不同的任务。

多线程的优势：

* (1)可以提高资源利用率，比如CPU资源利用率和内存资源利用率；
* (2)可以提高程序运行效率;
* (3)可以将耗时操作放到子线程中执行，防止阻塞主线程。

多线程的缺点：

* （1）开启线程需要占用一定的内存空间；
* （2）线程越多，CPU在调度线程上的开销就越大；
* （3）程序设计更加复杂:比如线程之间的通信,多线程的数据共享，需要考虑死锁、读写安全等问题。

**2.iOS的多线程方案有哪几种？你更倾向于哪一种？**

iOS的多线程方案有以下这几种：

* (1)pthread：是一套基于C语言的通用的多线程API，适用于Unix、Linux、Windows等系统，可跨平台移植，但是使用难度大；线程生命周期由程序员管理；从使用频率来看几乎不用。
* (2)NSThread：基于OC语言实现；使用更加面向对象，简单易用，可直接操作线程对象；线程生命周期由程序员管理；从使用频率来看偶尔使用。
* (3)GCD：基于C语言实现；旨在替代NSThread，充分利用设备的多核，执行效率更高；自动管理线程生命周期；从使用频率来看经常使用。
* (4)NSOperation：基于OC语言实现；底层是GCD，但比GCD多了一些更简单实用的功能；使用更加面向对象；自动管理线程生命周期；从使用频率来看经常使用。

更倾向于GCD和NSOPeration。

**3.用过GCD吗？在项目中具体是如何使用的？**

**4.GCD的队列类型有哪些？**

GCD的队列可以分为两大类型，分别是

* 串行队列(Serial Dispatch Queue)：串行队列是指让任务一个接着一个地执行的队列（一个任务执行完毕后，再执行下一个任务）。需要注意的是主队列(dispatch_queue queue = dispatch_get_main_queue();)其实也是串行队列。
* 并发队列(Concurrent Dispatch Queue)：并发队列是指可以让多个任务并发(同时)执行（自动开启多个线程同时执行任务）的队列。并发功能只有在异步(dispatch_async)函数下才有效。



**5.说一下OperationQueue和GCD的区别以及各自的优势**

**NSOperation和GCD的区别：**

* (1)从**底层实现**来看，GCD是基于C语言实现的系统服务，执行和操作简单高效；NSOperation是对GCD更高层次的抽象。GCG是iOS4.0推出的,主要针对多核CPU 做了优化；NSOperation 是 iOS2.0后推出的,iOS4.0之后重写了NSOperation。
* (2)从**依赖关系方面**来看，NSOperation可以设置两个NSOperation之间的依赖，方便的控制执行顺序；GCD无法直接设置这种依赖关系，不过GCD可以通过dispatch_barrier_async来实现这种效果；
* (3)**KVO(键值对观察)**，NSOperation可以容易监听判断Operation当前的状态(是否正在执行isExecuteing，是否取消isCancelled，是否已完成isFinished)，对此GCD无法通过KVO进行监听判断；
* (4)从设置**优先级方面**来看，NSOperation可以设置自身的优先级(但是优先级高的不一定先执行)；GCD只支持FIFO的队列，GCD只能设置队列的优先级，无法在执行的block设置优先级；
* (5)从**执行效率**方面来看，直接使用GCD效率会更高，NSOperation会多一点开销；
* (6)从**功能方面**来看，NSOperation可以方便地控制队列的停止/继续，也可以取消队列中所有的操作；而GCD不具备这些功能。

那么什么情况下使用NSOperation，什么情况下使用GCD呢？

**使用NSOperation的情况**：各个操作之间有依赖关系；操作需要取消暂停、并发管理；控制操作之间优先级；限制同时能执行的线程数量，让线程在某时刻停止/继续等。

**使用GCD的情况**：一般简单的多线程操作，都可以使用GCD，简单高效。

从编程原则来说，一般我们需要尽可能的使用高等级、封装完美的API，在必须时才使用底层的API。当需求简单，简洁的GCD或许是个更好的选择，而Operation queue 为我们提供了更多的选择。


**6.线程安全的处理手段有哪些？**

思路：详见“iOS中的10种线程同步方案”

**7.OC中的锁你了解哪些？使用以上这些锁需要注意哪些问题？自旋锁和互斥锁的异同？**

**(1)什么情况下使用自旋锁比较划算？**

* 预计线程等待锁的时间很短。
* 加锁的代码（临界区）经常被调用，但竞争情况很少发生。
* CPU资源不紧张
* 多核处理器

**(2)什么情况下使用互斥锁比较划算？**

* 预计线程等待锁的时间较长
* 单核处理器
* 临界区有IO操作（因为IO操作都占用CPU资源比较大）
* 临界区代码复杂或者循环量大
* 临界区竞争非常激烈

**[自旋锁和互斥锁的异同详见【扩展 10-8】自旋锁和互斥锁的异同？](https://github.com/baohenglin/iOS-Interview-Question/blob/master/Article/iOSInterviewQuestionsSummaryThree.md)**

**8.任选C/OC/C++其中一种语言，实现自旋锁和互斥锁？**

**9.performSelector: withObject: afterDelay:方法的本质是往RunLoop中添加定时器，子线程默认没有启动RunLoop。**

**10.GCD当中的时间单位**

1秒(s) =1000 毫秒(ms) = 1,000,000 微秒(μs) = 1,000,000,000 纳秒(ns)
             
* PER：每
* SEC：秒
* MSEC：毫秒
* USEC：微妙
* NSEC：纳秒

```
#define NSEC_PER_SEC 1000000000ull     //每秒有1000,000,000 纳秒
#define NSEC_PER_MSEC 1000000ull       //每毫秒有1000,000 纳秒
#define USEC_PER_SEC 1000000ull        //每秒有1000,000 微妙
#define NSEC_PER_USEC 1000ull          //每微妙有1000 纳秒
```


## GNUstep

GNUstep是GNU计划的项目之一，它将Cocoa的OC库重新实现了一遍并将其开源。虽然GNUstep不是苹果官方源码，但还是具有一定的参考价值。[GNUstep源码地址](http://www.gnustep.org/resources/downloads.php)


<br>
<br>
参考链接：
<br>

[不再安全的 OSSpinLock](https://blog.ibireme.com/2016/01/16/spinlock_is_unsafe_in_ios/)

[深入理解 iOS 开发中的锁](https://juejin.im/post/57f6e9f85bbb50005b126e5f)

[关于 @synchronized，这儿比你想知道的还要多](http://yulingtianxia.com/blog/2015/11/01/More-than-you-want-to-know-about-synchronized/)

[Threading Programming Guide](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/Introduction/Introduction.html#//apple_ref/doc/uid/10000057)

[pthread的各种同步机制](https://casatwy.com/pthreadde-ge-chong-tong-bu-ji-zhi.html)



