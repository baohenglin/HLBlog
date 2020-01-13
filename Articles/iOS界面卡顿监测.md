

对移动开发者而言，实时监控界面卡顿是分析和解决卡顿问题的重要方法。那么能否设计一套“实时监控和自动预报卡顿，并检测出导致该卡顿的代码具体位置”的解决方案呢？这正是本文将要研究的问题。

## 解决方案分析

简单来说，为了达到界面滑动流畅(不卡顿)的视觉体验，主线程需要达到接近 60 FPS 的绘制效率。也就是说，在刷新UI的主线程中不能有单个超过16ms（1s/60≈16ms）的计算任务。

通常**造成卡顿的原因**大致有以下几方面：

* 复杂UI、图文混排的绘制量过大；
* 在主线程上做网络同步请求；
* 在主线程做大量的 IO 操作；
* 运算量过大，CPU 持续高占用；
* 死锁和主子线程抢锁。

一般通过 Instrument 工具设置 16ms 的采样率可以检测出大部分这种耗时的任务，但是有以下几个缺点：

* (1)Instrument profile 一次重新编译，时间较长。
* (2)只能针对特定的操作场景进行检测，要预先知道卡顿产生的场景。
* (3)每次猜测，更改，再猜测再以此循环，需要重新profile。

我们的目标方案是能够自动实时监控卡顿且能立即输出并上报导致该卡顿的函数调用栈。显然 Instrument 不符。

目前想到的大致可行的方案有以下四种：

* (1)基于 RunLoop 的监控方案；
* (2)基于线程的 ping 监控方案（可参考[PMainThreadWatcher](https://github.com/music4kid/PMainThreadWatcher)）；
* (3)基于 FPS 的监控方案；
* (4)hook msgSend 方案；

四种方案性能对比：

||RunLoop|ping|FPS|hook msgSend|
|-|-|-|-|-|
|损耗|低，仅监控runloop状态|中，需要一个常驻子线程|中低，闲置时会频繁唤醒runloop处理|高，任意方法均会hook，处理量太大|
|精度|中高|高，卡顿时能准确获取到栈信息|低，需cpu空闲时才能回调，栈信息采集不够及时|极高，只要是方法耗时，均会拦截|
|反馈|中高，监控状态切换耗时，但timer、dispatchMain、source1事件可能反馈不到位|高，能有效收集主线程卡顿，且可以控制卡顿阈值|高，但在table滑动、转场动画等情况也会有下滑，会收集较多无用记录|极高，可能会采集到大量系统方法消耗|
|成本|中低，实现代码相对较多|低，常驻子线程ping主线程，及时释放临时变量|低，使用CADisplayLink实现|中高，依赖runtime，需使用OC编写|



## RunLoop 方案来监控卡顿

那么如何监控卡顿呢？是要监控FPS吗？监控FPS来判断卡顿是否准确呢？

FPS是一秒显示的帧数，也就是一秒内画面变化的数量。如果按照动画片来说，动画片的FPS就是24，是达不到60满帧的。24帧虽然没有60帧时流畅，但是也已经是连贯的了，所以并不能说24帧时就算卡顿了。

由此可见，简单地通过监测FPS是很难确定是否会出现卡顿问题，所以不推荐通过监视FPS来监控卡顿的方案。

我们可以**通过监听RunLoop的状态来监控卡顿**。监控卡顿其实就是要找出主线程都做了哪些事情。众所周知，线程的消息事件是依赖于NSRunLoop的，所以从NSRunLoop入手，就可以知道主线程上都调用了哪些方法。我们通过监听NSRunLoop的状态，就能够发现调用方法是否执行时间过长，从而判断是否出现了卡顿现象。

RunLoop这个对象在iOS里由CFRunLoop实现。简单来说，RunLoop是用来监听输入源，进行调度处理的。输入源可以是输入设备、网络、周期性或延迟时间、异步回调。RunLoop会接收两种类型的输入源：一种是来自另一个线程或者来自不同应用的异步消息；另一种是来自预定时间或者重复间隔的同步事件。

RunLoop的作用是当有事件要去处理时保持线程忙，当没有事件要处理时让线程进入休眠。所以，了解**RunLoop原理不仅可以运用到监控卡顿上，还可以提高用户的交互体验。通过将那些繁重而不紧急会大量占用CPU的任务（比如图片加载），放到空闲的RunLoop模式里执行，就可以避免在UITrackingRunLoopMode这个RunLoop模式里执行。UITrackingRunLoopMode是用户进行滑动操作时切换到的RunLoop模式，避免在这个RunLoop模式执行繁重的CPU任务，可以提高用户体验**。

## RunLoop原理

接下来，通过CFRunLoop源码来学习RunLoop的底层原理。

**第1步**：通知Observers，RunLoop要开始进入loop了。紧接着就进入loop。代码如下：

```
// 通知 observers
if (currentMode->_observerMask & kCFRunLoopEntry ) 
    __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopEntry);
// 进入 loop
result = __CFRunLoopRun(rl, currentMode, seconds, returnAfterSourceHandled, previousMode);
```

**第2步**：开启一个do while来保活线程。通知Observers：RunLoop会触发Timer回调、Source0回调，接着执行加入的block。代码如下：

```
// 通知 Observers RunLoop 会触发 Timer 回调
if (currentMode->_observerMask & kCFRunLoopBeforeTimers)
    __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopBeforeTimers);
// 通知 Observers RunLoop 会触发 Source0 回调
if (currentMode->_observerMask & kCFRunLoopBeforeSources)
    __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopBeforeSources);
// 执行 block
__CFRunLoopDoBlocks(runloop, currentMode);
```

接下来，触发Source0回调，如果有Source1是ready状态的话，就会跳转到handle_msg去处理消息。代码如下：

```
if (MACH_PORT_NULL != dispatchPort ) {
    Boolean hasMsg = __CFRunLoopServiceMachPort(dispatchPort, &msg)
    if (hasMsg) goto handle_msg;
}
```

**第3步**：回调触发后，通知Observers：RunLoop的线程将进入休眠（sleep）状态。代码如下：

```
Boolean poll = sourceHandledThisLoop || (0ULL == timeout_context->termTSR);
if (!poll && (currentMode->_observerMask & kCFRunLoopBeforeWaiting)) {
    __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopBeforeWaiting);
}
```

**第4步**：进入休眠后，会等待mach_port的消息，以再次唤醒。唤醒休眠线程的事件包括以下四个：

* 基于port的Source事件；
* Timer时间到；
* RunLoop超时；
* 被调用者唤醒。

等待唤醒的代码如下：

```
do {
    __CFRunLoopServiceMachPort(waitSet, &msg, sizeof(msg_buffer), &livePort) {
        // 基于 port 的 Source 事件、调用者唤醒
        if (modeQueuePort != MACH_PORT_NULL && livePort == modeQueuePort) {
            break;
        }
        // Timer 时间到、RunLoop 超时
        if (currentMode->_timerFired) {
            break;
        }
} while (1);
```

**第5步**：唤醒时通知Observer，RunLoop的线程刚刚被唤醒了。代码如下：

```
//通知Observer：RunLoop的线程已经被唤醒了。
if (!poll && (currentMode->_observerMask & kCFRunLoopAfterWaiting))
    __CFRunLoopDoObservers(runloop, currentMode, kCFRunLoopAfterWaiting);
```

**第6步**：RunLoop被唤醒后就要开始处理消息了。消息事件包括以下几种：

* 如果是Timer时间到了的话，就触发Timer的回调；
* 如果是dispatch的话，就执行block；
* 如果是source1事件的话，就处理这个事件。

消息执行完后，就执行加到loop里的block。代码如下：

```
handle_msg:
// 如果 Timer 时间到，就触发 Timer 回调
if (msg-is-timer) {
    __CFRunLoopDoTimers(runloop, currentMode, mach_absolute_time())
} 
// 如果 dispatch 就执行 block
else if (msg_is_dispatch) {
    __CFRUNLOOP_IS_SERVICING_THE_MAIN_DISPATCH_QUEUE__(msg);
} 

// Source1 事件的话，就处理这个事件
else {
    CFRunLoopSourceRef source1 = __CFRunLoopModeFindSourceForMachPort(runloop, currentMode, livePort);
    sourceHandledThisLoop = __CFRunLoopDoSource1(runloop, currentMode, source1, msg);
    if (sourceHandledThisLoop) {
        mach_msg(reply, MACH_SEND_MSG, reply);
    }
}
```

**第7步**：根据当前的RunLoop的状态来判断是否需要执行下一个loop。当被外部强制停止或loop超时时，就不继续执行下一个loop了，否则继续走下一个loop。代码如下：

```
if (sourceHandledThisLoop && stopAfterHandle) {
     // 事件已处理完
    retVal = kCFRunLoopRunHandledSource;
} else if (timeout) {
    // 超时
    retVal = kCFRunLoopRunTimedOut;
} else if (__CFRunLoopIsStopped(runloop)) {
    // 外部调用者强制停止
    retVal = kCFRunLoopRunStopped;
} else if (__CFRunLoopModeIsEmpty(runloop, currentMode)) {
    // mode 为空，RunLoop 结束
    retVal = kCFRunLoopRunFinished;
}
```

## RunLoop的6种状态

```
typedef CF_OPTIONS(CFOptionFlags, CFRunLoopActivity) {
    kCFRunLoopEntry , // 进入 loop
    kCFRunLoopBeforeTimers , // 触发 Timer 回调
    kCFRunLoopBeforeSources , // 触发 Source0 回调（进入休眠前的状态）
    kCFRunLoopBeforeWaiting , // 即将进入休眠，等待 mach_port 消息
    kCFRunLoopAfterWaiting  , // 接收 mach_port 消息（唤醒线程后的状态）
    kCFRunLoopExit , // 退出 loop
    kCFRunLoopAllActivities  // loop 所有状态改变
}
```

如果RunLoop的线程，**进入睡眠前方法的执行时间过长**而导致无法进入睡眠，或者**线程唤醒后接收消息时间过长**而无法进入下一步的话，就可以认为是线程受阻了。如果这个线程是主线程的话，表现出来的就是出现了卡顿。所以，如果我们要利用RunLoop原理来监控卡顿的话，就是要关注这两个阶段。RunLoop在进入睡眠之前和唤醒后的两个loop状态定义的值，分别是**kCFRunLoopBeforeSource**和**kCFRunLoopAfterWaiting**，也就是要触发Source0回调和接收mach_port消息的这两个状态。

**为什么监听kCFRunLoopBeforeSource和kCFRunLoopAfterWaiting这两个状态就能够监控卡顿呢，而不是kCFRunLoopBeforeWaiting和kCFRunLoopAfterWaiting这两种状态呢？**

因为RunLoop进入休眠之前(kCFRunLoopBeforeWaiting)一定会先执行 source0 等方法，唤醒(kCFRunLoopAfterWaiting)后要接收 mach_port 消息。如果在执行source0 或者接收 mach_port 消息的时候太耗时，那么就会导致卡顿。我们把 kCFRunLoopBeforeSources 作为执行Source0、Source1 等方法的开始时间节点，将 kCFRunLoopAfterWaiting 作为接收 mach_port 消息的开始时间节点，所以只需要监控这两个状态是否超过设定的时间阀值。而如果监控kCFRunLoopBeforeWaiting 状态的话，由于监听到 kCFRunLoopBeforeWaiting 状态时，其实已经执行完 source 事件，无法监控 source0 的耗时长短，故不能监听 kCFRunLoopBeforeWaiting 这个状态。

## 如何检查卡顿？

那么如何对loop的kCFRunLoopBefore和kCFRunLoopAfterWaiting这两个状态进行监听呢？此外，监听的时间值如何设置才合理呢？

首先，需要**创建一个CFRunLoopObserverContext观察者**，代码如下：

```
CFRunLoopObserverContext context = {0,(__bridge void*)self,NULL,NULL};
runLoopObserver = CFRunLoopObserverCreate(kCFAllocatorDefault,kCFRunLoopAllActivities,YES,0,&runLoopObserverCallBack,&context);
```

然后将创建好的观察者runLoopObserver添加到主线程RunLoop的common模式下观察。代码如下：

```
//将观察者添加到主线程runloop的common模式下的观察中
CFRunLoopAddObserver(CFRunLoopGetMain(), runLoopObserver, kCFRunLoopCommonModes);
```

紧接着，创建一个持续的子线程专门用来监控主线程的RunLoop状态。

一旦发现**进入睡眠前的kCFRunLoopBeforeSource状态或者唤醒后的状态kCFRunLoopAfterWaiting在设置的时间阀值内一直没有变化**，即可判定为**卡顿**。接下来，我们就可以dump出堆栈的信息，从而进一步分析出具体是哪个方法的执行时间过长。

开启一个子线程监控的代码如下：

```
// 创建子线程监控
dispatch_async(dispatch_get_global_queue(0, 0), ^{
    // 子线程开启一个持续的 loop 用来进行监控
    while (YES) {
        long semaphoreWait = dispatch_semaphore_wait(dispatchSemaphore, dispatch_time(DISPATCH_TIME_NOW, 3 * NSEC_PER_SEC));
        if (semaphoreWait != 0) {
            if (!runLoopObserver) {
                timeoutCount = 0;
                dispatchSemaphore = 0;
                runLoopActivity = 0;
                return;
            }
            //kCFRunLoopBeforeSource 和 kCFRunLoopAfterWaiting 这两个状态能够检测到是否卡顿
            if (runLoopActivity == kCFRunLoopBeforeSources || runLoopActivity == kCFRunLoopAfterWaiting) {
                // 将堆栈信息上报服务器的代码放到这里
            } //end activity
        }// end semaphore wait
        timeoutCount = 0;
    }// end while
});
```

上述代码中的NSEC_PER_SEC表示触发卡顿的时间阀值，单位是秒。此处，我们将时间阀值设置为 3 * NSEC_PER_SEC ，也就是3秒，那么为什么将时间阀值设置为3秒？这样设置合理吗？

其实，触发卡顿的时间阀值，我们可以根据WatchDog机制来设置。WatchDog在不同状态下设置的不同时间。如下所示：

* 启动（Launch）：20s；
* 恢复（Resume）：10s；
* 挂起（Suspend）：10s；
* 退出（Quit）：6s；
* 后台（Background）：3min(在iOS7之前，每次申请10min；之后改为每次申请3min，可连续申请，最多申请到10min)。

通过WatchDog设置的时间，可以把启动的阀值设置为10秒，其他状态则都默认设置为3秒。总的原则就是，要小于WatchDog的限制时间。当然了，这个阀值也不用小得太多，原则就是要优先解决用户感知最明显的体验问题。

【注意】虽然总的原则是必须要小于WatchDog的限制时间，但是从实践来看，可以将**监控卡顿的时间阀值设置为20ms**。

## 如何获取卡顿的方法堆栈信息？

子线程监控发现主线程卡顿后，还需要记录当前出现卡顿的方法堆栈信息，并适时推送到服务端供开发者分析，从而解决卡顿问题。那么到底该如何获取卡顿的方法堆栈信息呢？

**获取堆栈信息的一种方法是直接调用系统函数**。这种方法的优点在于，性能消耗小。但是这种方法只能获取简单的信息，也没有办法配合dSYM来获取具体是哪行代码出了问题，而且能够获取的信息类型也有限。这种方法，因为性能比较好，所以适用于观察大盘统计卡顿情况，而不是想要找到卡顿原因的场景。

直接调用系统函数方法的主要思路是：用signal进行错误信息的获取。具体代码如下：

```
static int s_fatal_signals[] = {
    SIGABRT,
    SIGBUS,
    SIGFPE,
    SIGILL,
    SIGSEGV,
    SIGTRAP,
    SIGTERM,
    SIGKILL,
};

static int s_fatal_signal_num = sizeof(s_fatal_signals) / sizeof(s_fatal_signals[0]);

void UncaughtExceptionHandler(NSException *exception) {
    NSArray *exceptionArray = [exception callStackSymbols]; // 得到当前调用栈信息
    NSString *exceptionReason = [exception reason];       // 非常重要，就是崩溃的原因
    NSString *exceptionName = [exception name];           // 异常类型
}

void SignalHandler(int code)
{
    NSLog(@"signal handler = %d",code);
}

void InitCrashReport()
{
    // 系统错误信号捕获
    for (int i = 0; i < s_fatal_signal_num; ++i) {
        signal(s_fatal_signals[i], SignalHandler);
    }
    
    //oc 未捕获异常的捕获
    NSSetUncaughtExceptionHandler(&UncaughtExceptionHandler);
}

int main(int argc, char * argv[]) {
    @autoreleasepool {
        InitCrashReport();
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
```

**另一种方法是直接用PLCrashReporter这个开源的第三方库来获取堆栈信息。** 这种方法的优势在于，能够定位到问题代码的具体位置，而且性能消耗也不大。所以推荐使用此方法。

具体如何使用PLCrashReporter来获取堆栈信息，代码如下：

```
PLCrashReporterConfig *config = [[PLCrashReporterConfig alloc] initWithSignalHandlerType:PLCrashReporterSignalHandlerTypeBSD symbolicationStrategy:PLCrashReporterSymbolicationStrategyAll];
PLCrashReporter *crashReporter = [[PLCrashReporter alloc] initWithConfiguration:config];
NSData *data = [crashReporter generateLiveReport];
PLCrashReport *reporter = [[PLCrashReport alloc] initWithData:data error:NULL];
NSString *report = [PLCrashReportTextFormatter stringValueForCrashReport:reporter withTextFormat:PLCrashReportTextFormatiOS];
NSLog(@"---------卡顿信息\n%@\n--------------",report);
```

搜集到卡顿的方法堆栈信息以后，开发者可以根据搜集到的具体日志信息来分析并解决卡顿问题。


## 测试用例

当滑动tableView时，人为设置卡顿(休眠)，来测试我们实时监控卡顿的代码是否有效。

```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{
    static NSString *identifierStr = @"mycell";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:identifierStr];
    if (cell == nil) {
        cell = [[UITableViewCell alloc]initWithStyle:UITableViewCellStyleDefault reuseIdentifier:identifierStr];
    }
    NSString *cellText = nil;
    if (indexPath.row % 5 == 0) {
        //滑动列表时，人为设置卡顿(休眠)，来测试我们实时监控卡顿的代码是否有效。
        //每5行休眠0.05s（50ms）
        usleep(50 * 1000);
        cellText = @"休眠：做一些耗时操作";
    } else {
        cellText = [NSString stringWithFormat:@"cell - %ld",indexPath.row];
    }
    cell.textLabel.text = cellText;
    return cell;
}
```

## 实测数据

[完整demo代码](https://github.com/baohenglin/Real-time-monitoring-of-Caton-with-RunLoop)


以下是通过iPhone6s真机测试，获得的真实数据：

![卡顿次数实时数据.png](https://upload-images.jianshu.io/upload_images/4164292-00bb31df1f7fa323.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**数据分析：**

当RunLoop的kCFRunLoopBeforeWaiting和kCFRunLoopAfterWaiting这两个状态当中的任何一个状态持续时间超过20ms时，都会打印一次连续卡顿的次数；当连续卡顿的次数累积达到3次时，将被视为一次有效卡顿，并输出卡顿的详细信息。

通过PLCrashReporter捕获到的卡顿详细信息，如下所示：

![卡顿信息.png](https://upload-images.jianshu.io/upload_images/4164292-4a9ddd52171bf149.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由测试数据可知，我们监控卡顿并通过PLCrashReporter捕获函数调用栈详细信息的代码是有效的。


【注意】本篇文章是学习戴铭老师的 [《如何利用 RunLoop 原理去监控卡顿》](https://time.geekbang.org/column/article/89494)这篇文章所整理的学习笔记。
















































**参考链接**

* [iOS实时卡顿监控](http://www.tanhao.me/code/151113.html/)
* [解密 Runloop](http://mrpeak.cn/blog/ios-runloop/)
* [微信iOS卡顿监控系统](https://mp.weixin.qq.com/s?__biz=MzAwNDY1ODY2OQ==&mid=207890859&idx=1&sn=e98dd604cdb854e7a5808d2072c29162&scene=4)
* [卡顿监测-FPS监测](https://juejin.im/entry/5c8cc988e51d4552775db9d2)
* [RunLoop监控卡顿](https://juejin.im/post/5cacb2baf265da03904bf93b)
