# 第 31 条：在 dealloc 方法中只释放引用并解除监听

对象在经历其生命期后，最终会为系统所回收，这时就要执行 dealloc 方法了。在每个对象的生命期内，此方法**仅执行一次**，也就是当保留计数降为 0 的时候。然而具体何时执行，则无法保证。也可以理解成：我们能够通过人工观察保留操作与释放操作的位置，来预计此方法何时即将执行。但实际上，程序库会以开发者觉察不到的方式操作对象，从而使回收对象的真正时机和预期的不同。**开发者勿手动调用 dealloc 方法**。运行期系统会在适当的时候调用它。而且，一旦调用过 dealloc 之后，对象就不再有效了，后续方法调用均是无效的。

那么，应该在 dealloc 方法中做些什么呢？主要是**释放所拥有的引用**（用途1），也就是**把所有 Objective-C 对象都释放掉**，ARC 会通过自动生成的 .cxx_destruct 方法（参见第 30 条），在 dealloc 中为你自动添加这些释放代码。**对象所拥有的其他非 Objective-C 对象也要释放**（用途2）。比如 CoreFoundation 对象就必须手动释放，因为它们是由纯 C 的 API 所生成的。

在 dealloc 方法中，通常还要做一件事，那就是把原来配置过的观测行为（observation behavior）都清理掉（用途3：**注销通知**）。如果用 NSNotificationCenter 给此对象订阅（register）过某种通知，那么一般应该在这里注销（unregister），这样的话，通知系统就不再把通知发给回收后的对象了，若是还向其发送通知，则必然会令应用程序崩溃。

dealloc 方法可以这样来写：

```
- (void)dealloc {
  CFRelease(coreFoundationObject); //释放对象所拥有的其他非 Objective-C 对象
  [[NSNotificationCenter defaultCenter] removeObserver:self]; //注销通知
}
```

需要注意的是，**如果手动管理引用计数而不使用 ARC 的话，那么最后还需要调用“[super dealloc]”。ARC会自动执行此操作，这再次表明其比手动管理更简单、更安全**。若选择手动管理，则还要将当前对象所拥有的全部 Objective-C 对象逐个释放。

虽说应该于 dealloc 中释放引用，**但是开销较大或系统内稀缺的资源则不在此列。像是文件描述符（file descriptor）、套接字（socket）、大块内存等，都属于这种资源**。不能指望 dealloc 方法必定会在某个特定的时机调用，因为有一些无法预料的东西可能也持有此对象。在这种情况下，如果非要等到系统调用 dealloc 方法时才释放，那么保留这些稀缺资源的时间就有些过长了，这么做不合适。**通常的做法是，实现另外一个方法，当应用程序用完资源对象后，就调用此方法**。这样一来，资源对象的生命周期就变得更为明确了。

比如说，如果某对象管理着连接服务器所用的套接字，那么也许就需要这种“清理方法”（cleanup method）。此对象可能要通过套接字连接到数据库。对于对象所属的类，其接口可以这样写：

```
#import <Foundation/Foundation.h>

@interface EOCServerConnection : NSObject
- (void)open: (NSString *)address;
- (void)close;
@end
```

该类与开发者之间的约定是：想打开连接，就调用 open 方法；连接使用完毕，就调用 close 方法。**“关闭”操作必须在系统把连接对象回收之前调用**，否则就是编程错误（programmer error），这与通过“保留”及“释放”操作来平衡引用计数是类似的。

在清理方法而非 dealloc 方法中清理资源还有个原因，就是**系统并不保证每个创建出来的对象的 dealloc 都会执行**。极个别情况下，当应用程序终止时，仍有对象处于存活状态，这些对象没有收到 dealloc 消息。由于应用程序终止之后，其占用的资源也会返还给操作系统，所以实际上这些对象也就等于是消亡了。不调用 dealloc 方法是为了优化程序效率。而这也说明系统未必会在每个对象上调用其 dealloc 方法。

在 Mac OS X及iOS应用程序所对应的 application delegate 中，都含有一个会于程序终止时调用的方法。如果一定要清理某些对象，那么可在此方法中调用那些对象的“清理方法”。

在 Mac OS X 系统里，应用程序终止时会调用 NSApplicationDelegate 之中的下述方法：

```
- (void)applicationWillTerminate:(NSNotication *)notification
```

而在 iOS 系统里，应用程序终止时则会调用 UIApplicationDelegate 之中的下列方法：

```
- (void)applicationWillTerminate:(UIApplication *)application
```

如果对象管理着某些资源，那么在 dealloc 中也要调用“清理方法”，以防开发者忘了清理这些资源。忘记清理资源的情况经常会发生，所以最好能输出一行消息，提示程序员代码里含有编程错误。在系统回收对象之前，必须调用 close 以释放其资源，否则 close 方法就失去意义了，因此，没有适时调用 close 方法就是编程错误。输出错误消息可促使开发者纠正此问题。而且，在程序员忘记调用close的情况下，我们应该在 dealloc 






