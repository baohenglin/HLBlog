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


### 死锁

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



## 多线程总结

* 1.简述你对多线程的理解

* 2.iOS的多线程方案有哪几种？你更倾向于哪一种？

* 3.用过GCD吗？在项目中具体是如何使用的？

* 4.GCD的队列类型有哪些？

* 5.说一下OperationQueue和GCD的区别以及各自的优势

* 6.线程安全的处理手段有哪些？

* 7.OC中的锁你了解哪些？使用以上这些锁需要注意哪些问题？自旋锁和互斥锁的异同？

* 8.任选C/OC/C++其中一种语言，实现自旋锁和互斥锁？
