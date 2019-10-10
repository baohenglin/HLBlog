# App启动速度的监控与优化

一般来说，App 的启动分为冷启动和热启动两种。那么什么是冷启动？什么是热启动呢？

* **冷启动**是指App点击启动前，它的进程不在系统里，需要系统新创建一个进程分配给它启动的情况。这是一次完整的启动过程。
* **热启动**是指App在冷启动之后用户将App退到后台，在 App 的进程还在系统里的情况下，用户重新启动进入 App 的过程。

冷启动相比热启动执行的操作要多得多，所以我们此处只论述针对冷启动的监控与优化。用户能感知到的启动速度慢，其实都发生在主线程上。而主线程慢的原因有很多，比如在主线程上执行了大文件读写操作、在渲染周期中执行了大量计算等。那么如何将启动时所有耗时操作都找出来呢？为了解决此问题，我们需要首先弄清楚App在启动时都做了什么。

App的启动过程主要分为三个阶段：

* (1) main()函数执行前；
* (2) main()函数执行后；
* (3) 首屏渲染完成后。


## main() 函数执行前

在执行 main()函数前，系统做的事情包括：

* 加载可执行文件（App的.o文件的集合）；
* 加载动态链接库，进行 rebase 指针调整和 bind 符号绑定；
* Objc 运行时的初始处理，包括Objc相关类的注册、category 注册、selector唯一性检查等；
* 初始化，包括了执行 +load() 方法、attribute((constructor))修饰的函数的调用、创建 C++静态全局变量。

这个阶段可以从下面几个方面来进行启动速度优化：

* 减少动态库加载。每个库本身都有依赖关系，苹果公司建议使用更少的动态库，并且建议在使用动态库的数量较多时，尽量将多个动态库进行合并。数量上，苹果公司最多可以支持6个非系统动态库合并为一个。
* 减少加载启动后不会去使用的类或者方法。
* +load()方法里的内容可以放到首屏渲染完成后再执行，或者用 +initialize()方法替换掉。因为在一个 +load()方法里，进行运行时方法替换操作会带来4毫秒的时间消耗。
* 控制 C++ 全局变量的数量。

## main()函数执行后

main()函数执行后的阶段，指的是从main()函数执行开始，到appDelegate的didFinishLaunchingWithOptions 方法里首屏相关方法执行完成。

首页的业务代码都是要在这个阶段，也就是首屏渲染前执行的，主要包括了：

* 首屏初始化所需配置文件的读写操作；
* 首屏列表大数据的读取；
* 首屏渲染的大量计算等。

很多时候，开发者会把各种初始化工作放到这个阶段执行，导致渲染完成滞后。更加优化的做法应该是从功能上梳理出哪些是首屏渲染必要的初始化功能，哪些是App启动必要的初始化功能，而哪些是只需要在对应功能开始使用时才需要初始化的。梳理完之后，将这些初始化功能分别放到合适的阶段执行。


## 首屏渲染完成后

首屏渲染后的这个阶段，主要完成的是，非首屏其他业务服务模块的初始化、监听的注册、配置文件的读取等。从函数上来看，这个阶段指的就是截止到 didFinishLaunchingWithOptions 方法作用域内执行首屏渲染之后的所有方法执行完成。简单来说的话，这个阶段就是从渲染完成时开始，到 didFinishLaunchingWithOptions 方法作用域结束时结束。

这个阶段用户已经能够看到App的首页信息了，所以优化的优先级排在最后。但是，那些会卡住主线程的方法还是需要最优先处理的，不然还是会影响到用户后面的交互操作。

梳理清楚App启动的整个流程后，下面我们从功能级别和方法级别这两个角度来阐述启动速度的优化。

## 功能级别的启动优化

功能级别的启动优化，就是要从 main() 函数执行后这个阶段下手。

**优化的思路**是：main() 函数开始执行后到首屏渲染完成前只处理与首屏相关的业务(首屏相关基础库的初始化，首屏相关业务数据读取和处理)，其他非首屏业务的初始化、监听注册、配置文件读取等都放到首屏渲染完成后去做。

## 方法级别的启动优化

经过功能级别的启动优化，也就是将非首屏业务所需的功能滞后执行之后，从用户点击App到看到首屏的时间将会有很大程度的缩短，也就达到了优化App启动速度的目的。在这之后，我们需要进一步做的是检查首屏渲染完成前主线程上有哪些耗时方法，将没有必要的耗时方法滞后或者异步执行。通常情况下，耗时较长的方法主要发生在计算大量数据的情况下，比如加载、编辑、存储图片和文件等资源。

耗时方法有很多。比如 +load()方法，一个耗时4毫秒，100个就是400毫秒，这种耗时用户也是能明显感知到的。再比如ReactiveCocoa框架（响应式编程框架），每创建一个信号都有6毫秒的耗时。这样，稍不注意各种信号的创建就都被放在了首屏渲染完成前，进而导致App的启动速度大幅变慢。

类似这样单个方法耗时不多，但是由于堆积导致App启动速度大幅变慢的方法数不胜数。那么我们可以采取哪些监控手段来对启动方法的耗时进行全面、精确的监控呢？这些监控方案各有什么优缺点？

目前来看，对App启动速度的监控，主要有两种方法。

**第一种方法是，定时抓取主线程上的方法调用堆栈，计算一段时间里各个方法的耗时**。Xcode工具套件里自带的 Time Profiler，采用的就是这种方式。这种方法的优点是，开发类似工具成本不高，能够快速开发后集成到你的App中，以便在真实环境中进行检查。

说到定时抓取，就会涉及到定时间隔的长短问题。如果定时间隔设置得长了，就会漏掉一些方法，从而导致检查出来的耗时不精确。如果定时间隔设置得短了，抓取堆栈这个方法本身调用过多也会影响整体耗时，导致结果不准确。这个定时间隔如果小于所有方法执行的时间（比如0.002秒），那么基本就能监控到所有方法。但这样做的话，整体的耗时时间就不够准确。一般将这个定时间隔设置为0.01秒。这样设置，对整体耗时的影响小，不过很多方法耗时就不精确了。但因为整体耗时的数据更加重要些，单个方法耗时精度不高也是可以接受的。

总的来说，定时抓取主线程调用栈的方式虽然精准度不够高，但也是够用的。

**第二种方法是，对objc_msgSend方法进行 hook 来掌握所有方法的执行耗时**。hook方法的意思是，在原方法开始执行时换成执行其他指定的方法，或者在原有方法执行前后执行指定的方法，来达到掌握和改变指定方法的目的。hook objc_msgSend这种方式的优点是非常精确，而缺点是只能针对 Objective-C的方法。虽然对于C方法和block可以使用libffi的ffi_call来达成hook，但是编写维护工具门槛高。

综上，如果对于检查结果精准度要求高的话，推荐使用 hook objc_msgSend的方式来检查启动方法的执行耗时。

## 如何开发一个方法级别的启动耗时检查工具来辅助分析和监控？

使用 hook objc_msgSend方式来检查启动方法的执行耗时时，我们需要实现一个启动时间的监控工具。

**为什么说 hook 了 objc_msgSend方法，就可以 hook 全部的Objective-C的方法呢**？这是因为 Objective-C里每个对象都会指向一个类，每个类都会有一个方法列表，方法列表里的每个方法都是由 selector、函数指针和 metadata组成的。objc_msgSend方法的作用就是在运行时根据对象和方法的 selector 去查找对应的函数指针，然后执行该函数。也就是说 objc_msgSend是 Objective-C里方法执行的必经之路，所以hook Objc_msgSend能够监控所有的 Objective-C的方法。

objc_msgSend本身是用汇编语言编写的，原因有两个：(1)objc_msgSend的调用频次最高，在它上面进行的性能优化能够提升整个App生命周期的性能。而汇编语言在性能优化上属于原子级优化，能够把优化做到极致。(2)其他语言难以实现未知参数跳转到任意函数指针的功能。

[苹果公司objc_msgSend源码](https://opensource.apple.com/source/objc4/objc4-723/runtime/Messengers.subproj/objc-msg-arm64.s.auto.html)

objc_msgSend方法执行的逻辑是：先获取对象对应类的信息，再获取方法的缓存，根据方法的selector查找函数指针，经过异常错误处理后，最后跳转到对应函数的实现。


objc_msgSend方法执行的逻辑是：先获取对象对应类的信息，再获取方法的缓存，根据方法的selector查找函数指针，经过异常错误处理后，最后跳转到对应函数的实现。

下面，我们再看看怎么 hook objc_msgSend 方法？Facebook开源了一个库(fishhook)，可以在iOS上运行的 Mach-O 二进制文件中动态地重新绑定符号。[fishhook源码](https://github.com/facebook/fishhook)

fishhook实现的大致思路是，通过重新绑定符号，可以实现对 C 方法的 hook。dyld是通过更新 Mach-O 二进制的  __ DATA segment 特定的部分中指针来绑定 lazy 和 non-lazy 符号，通过确认传递给 rebind_symbol 里每个符号名称更新的位置，就可以找出对应替换来重新绑定这些符号。

只依靠fishhook无法完成 hook objc_msgSend 的全部任务，我们**需要实现两个方法 pushCallRecord 和 popCallRecord，来分别记录 objc_msgSend 方法调用前后的时间，然后相减就能够得到某方法的执行耗时**。

下面针对 arm64 架构，编写一个可保留未知参数并跳转到 c 中任意函数指针的汇编代码，实现对 objc_msgSend的 hook。arm64 有31个64 bit 的整数型寄存器，分别用 x0到 x30 表示。主要的实现思路是：

* 1. 入栈参数，参数寄存器是 x0~x7。对于 objc_msgSend 方法来说，x0第一个参数是传入对象，x1第二个参数是选择器 _ cmd。syscall 的 number 会放到 x8里。
* 2. 交换寄存器中保存的参数，将用于返回的寄存器 lr 中的数据移到 x1 里。
* 3. 使用 bl label 语法调用 pushCallRecord 函数。
* 4. 执行原始的 objc_msgSend，保存返回值。
* 5. 使用 bl label 语法调用 popCallRecord 函数。

具体的汇编代码，如下图所示：

```
static void replacementObjc_msgSend() {
  __asm__ volatile (
    // sp 是堆栈寄存器，存放栈的偏移地址，每次都指向栈顶。
    // 保存 {q0-q7} 偏移地址到 sp 寄存器
      "stp q6, q7, [sp, #-32]!\n"
      "stp q4, q5, [sp, #-32]!\n"
      "stp q2, q3, [sp, #-32]!\n"
      "stp q0, q1, [sp, #-32]!\n"
    // 保存 {x0-x8, lr}
      "stp x8, lr, [sp, #-16]!\n"
      "stp x6, x7, [sp, #-16]!\n"
      "stp x4, x5, [sp, #-16]!\n"
      "stp x2, x3, [sp, #-16]!\n"
      "stp x0, x1, [sp, #-16]!\n"
    // 交换参数.
      "mov x2, x1\n"
      "mov x1, lr\n"
      "mov x3, sp\n"
    // 调用 preObjc_msgSend，使用 bl label 语法。bl 执行一个分支链接操作，label 是无条件分支的，是和本指令的地址偏移，范围是 -128MB 到 +128MB
      "bl __Z15preObjc_msgSendP11objc_objectmP13objc_selectorP9RegState_\n"
      "mov x9, x0\n"
      "mov x10, x1\n"
      "tst x10, x10\n"
    // 读取 {x0-x8, lr} 从保存到 sp 栈顶的偏移地址读起
      "ldp x0, x1, [sp], #16\n"
      "ldp x2, x3, [sp], #16\n"
      "ldp x4, x5, [sp], #16\n"
      "ldp x6, x7, [sp], #16\n"
      "ldp x8, lr, [sp], #16\n"
    // 读取 {q0-q7}
      "ldp q0, q1, [sp], #32\n"
      "ldp q2, q3, [sp], #32\n"
      "ldp q4, q5, [sp], #32\n"
      "ldp q6, q7, [sp], #32\n"
      "b.eq Lpassthrough\n"
    // 调用原始 objc_msgSend。使用 blr xn 语法。blr 除了从指定寄存器读取新的 PC 值外效果和 bl 一样。xn 是通用寄存器的 64 位名称分支地址，范围是 0 到 31
      "blr x9\n"
    // 保存 {x0-x9}
      "stp x0, x1, [sp, #-16]!\n"
      "stp x2, x3, [sp, #-16]!\n"
      "stp x4, x5, [sp, #-16]!\n"
      "stp x6, x7, [sp, #-16]!\n"
      "stp x8, x9, [sp, #-16]!\n"
    // 保存 {q0-q7}
      "stp q0, q1, [sp, #-32]!\n"
      "stp q2, q3, [sp, #-32]!\n"
      "stp q4, q5, [sp, #-32]!\n"
      "stp q6, q7, [sp, #-32]!\n"
    // 调用 postObjc_msgSend hook.
      "bl __Z16postObjc_msgSendv\n"
      "mov lr, x0\n"
    // 读取 {q0-q7}
      "ldp q6, q7, [sp], #32\n"
      "ldp q4, q5, [sp], #32\n"
      "ldp q2, q3, [sp], #32\n"
      "ldp q0, q1, [sp], #32\n"
    // 读取 {x0-x9}
      "ldp x8, x9, [sp], #16\n"
      "ldp x6, x7, [sp], #16\n"
      "ldp x4, x5, [sp], #16\n"
      "ldp x2, x3, [sp], #16\n"
      "ldp x0, x1, [sp], #16\n"
      "ret\n"
      "Lpassthrough:\n"
    // br 无条件分支到寄存器中的地址
      "br x9"
    );
}
```

现在，我们就可以获取到每个 Objective-C 方法的耗时了。接下来我们再来研究怎样才能记录和展示方法调用的层级关系和顺序？

**第一步，设计两个结构体 CallRecord 和 ThreadCallStack**。其中 CallRecord 记录调用方法详细信息，包括 obj 和 SEL等；ThreadCallStack 里面，需要用 index 记录当前调用方法树的深度。有了 SEL 再通过 NSStringFromSelector 就能够获得方法名，有了 obj 通过 object_getClass 能够得到 Class，再用 NSStringFromClass 就能够获得类名。

代码如下：

```
// Shared structures.
typedef struct CallRecord_ {
  id obj;   // 通过 object_getClass 能够得到 Class 再通过 NSStringFromClass 能够得到类名
  SEL _cmd; // 通过 NSStringFromSelector 方法能够得到方法名
  uintptr_t lr;
  int prevHitIndex;
  char isWatchHit;
} CallRecord;
typedef struct ThreadCallStack_ {
  FILE *file;
  char *spacesStr;
  CallRecord *stack;
  int allocatedLength;
  int index; //index 记录当前调用方法树的深度
  int numWatchHits;
  int lastPrintedIndex;
  int lastHitIndex;
  char isLoggingEnabled;
  char isCompleteLoggingEnabled;
} ThreadCallStack;

```

**第二步，pthread_setspecific()可以将私有数据设置在指定线程上，pthread_getspecific()用来读取这个私有数据。**利用这个特性，我们就可以将 ThreadCallStack 的数据和该线程绑定在一起，随时进行数据存取。代码如下：

```
static inline ThreadCallStack * getThreadCallStack() {
  ThreadCallStack *cs = (ThreadCallStack *)pthread_getspecific(threadKey); // 读取
  if (cs == NULL) {
    cs = (ThreadCallStack *)malloc(sizeof(ThreadCallStack));
#ifdef MAIN_THREAD_ONLY
    cs->file = (pthread_main_np()) ? newFileForThread() : NULL;
#else
    cs->file = newFileForThread();
#endif
    cs->isLoggingEnabled = (cs->file != NULL);
    cs->isCompleteLoggingEnabled = 0;
    cs->spacesStr = (char *)malloc(DEFAULT_CALLSTACK_DEPTH + 1);
    memset(cs->spacesStr, ' ', DEFAULT_CALLSTACK_DEPTH);
    cs->spacesStr[DEFAULT_CALLSTACK_DEPTH] = '\0';
    cs->stack = (CallRecord *)calloc(DEFAULT_CALLSTACK_DEPTH, sizeof(CallRecord)); // 分配 CallRecord 默认空间
    cs->allocatedLength = DEFAULT_CALLSTACK_DEPTH;
    cs->index = cs->lastPrintedIndex = cs->lastHitIndex = -1;
    cs->numWatchHits = 0;
    pthread_setspecific(threadKey, cs); // 保存数据
  }
  return cs;
}
```

**第三步，因为要记录深度，而一个方法的调用里面会有更多的方法调用，所以我们可以在方法的调用里增加两个方法 pushCallRecord 和 popCallRecord，分别记录方法调用的开始时间和结束时间，这样才能够在开始时对深度加1，在结束时减1**。代码如下：

```
// 开始时
static inline void pushCallRecord(id obj, uintptr_t lr, SEL _cmd, ThreadCallStack *cs) {
  int nextIndex = (++cs->index); // 增加深度
  if (nextIndex >= cs->allocatedLength) {
    cs->allocatedLength += CALLSTACK_DEPTH_INCREMENT;
    cs->stack = (CallRecord *)realloc(cs->stack, cs->allocatedLength * sizeof(CallRecord));
    cs->spacesStr = (char *)realloc(cs->spacesStr, cs->allocatedLength + 1);
    memset(cs->spacesStr, ' ', cs->allocatedLength);
    cs->spacesStr[cs->allocatedLength] = '\0';
  }
  CallRecord *newRecord = &cs->stack[nextIndex];
  newRecord->obj = obj;
  newRecord->_cmd = _cmd;
  newRecord->lr = lr;
  newRecord->isWatchHit = 0;
}
// 结束时
static inline CallRecord * popCallRecord(ThreadCallStack *cs) {
  return &cs->stack[cs->index--]; // 减少深度
}
```

耗时监控的完整代码，请查看[戴铭老师的开源项目](https://github.com/ming1016/GCDFetchFeed)。在需要检测耗时时间的位置调用 [SMCallTrace start]，结束时调用 stop 和 save 就可以打印出方法的调用层级和耗时了。还可以设置最大深度和最小耗时检测，来过滤不需要看到的信息。



































