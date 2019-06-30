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

* 采用异步的方式执行任务

```
dispatch_async(dispatch_queue_t queue, dispatch_block_t block);
```

## 多线程总结

* 1.简述你对多线程的理解

* 2.iOS的多线程方案有哪几种？你更倾向于哪一种？

* 3.用过GCD吗？在项目中具体是如何使用的？

* 4.GCD的队列类型有哪些？

* 5.说一下OperationQueue和GCD的区别以及各自的优势

* 6.线程安全的处理手段有哪些？

* 7.OC中的锁你了解哪些？使用以上这些锁需要注意哪些问题？自旋锁和互斥锁的异同？

* 8.任选C/OC/C++其中一种语言，实现自旋锁和互斥锁？
