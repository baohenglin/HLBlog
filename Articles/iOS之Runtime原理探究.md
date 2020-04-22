# iOS之Runtime 原理探究


Objective-C 语言是一门动态性比较强的语言，与C、C++有很大不同。OC语言可以做到在程序运行时动态修改之前编译好的代码逻辑。也可以动态地添加某些方法的实现。

Objective-C 的动态性是由 Runtime API来支撑和实现的。Runtime 即运行时，它提供了一套C语言的API。Rumtime 的源码是开源的，Runtime 的源码是基于C/C++/汇编语言编写的。

Apple源码下载地址：<https://opensource.apple.com/tarballs/objc4/>

## isa详解-位域

在 arm64 架构之前，isa 就是一个普通的指针，存放着 Class 对象（类对象）或者 Meta-Class 对象（元类对象）的内存地址。

但是**从arm64架构开始，苹果对isa进行了优化，采用了一个共用体(union)结构，并且使用了“位域”技术。这样处理的好处是可以让一个64bit的内存空间存储了更多的信息，其中33bit用来存储 Class 对象或 Meta-Class 对象的内存地址。这也就是从arm64开始isa指针只有"&ISA_MASK"才能得到Class对象或Meta-Class对象的地址值的原因**(利用位运算)。


![&ISA_MASK.png](https://upload-images.jianshu.io/upload_images/4164292-153631fd8c38f12d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

共用体结构源码如下：

```
//共用体结构
union isa_t {
    isa_t() { }
    isa_t(uintptr_t value) : bits(value) { }

    Class cls;
    uintptr_t bits;
#if defined(ISA_BITFIELD)
    struct {
        ISA_BITFIELD;  // defined in isa.h
    };
#endif
};
```

宏定义“#define ISA_BITFIELD”在arm64架构下的源码如下：

```
//结构体支持“位域”。 
#   define ISA_BITFIELD                                                      \
      uintptr_t nonpointer        : 1;                                       \
      uintptr_t has_assoc         : 1;                                       \
      uintptr_t has_cxx_dtor      : 1;                                       \
      uintptr_t shiftcls          : 33; /*MACH_VM_MAX_ADDRESS 0x1000000000*/ \
      uintptr_t magic             : 6;                                       \
      uintptr_t weakly_referenced : 1;                                       \
      uintptr_t deallocating      : 1;                                       \
      uintptr_t has_sidetable_rc  : 1;                                       \
      uintptr_t extra_rc          : 19
```

共用体的概念：所有成员变量共用同一块内存。

结构体ISA_BITFIELD中各个参数的含义如下：

* nonpointer：0代表普通的指针，存储着Class、Meta-Class对象的内存地址；1代表优化过，使用"位域存"储更多信息。
* has_assoc：是否曾经设置过关联对象。如果没有，释放时会更快。
* has_cxx_dtor：是否有C++的析构函数（.cxx_destruct,C++的析构函数类似于OC的dealloc方法），如果没有，释放时会更快。
* **shiftcls**：存储着Class 对象、Meta-Class 对象的内存地址信息，最后面的 3bit 一定是0。
* magic：用于在调试时分辨对象是否未完成初始化。
* weakly_referenced：是否曾经被弱引用指向过。如果没有，释放时会更快。
* deallocating：标记对象是否正在释放。
* has_sidetable_rc：引用计数器是否过大无法存储在isa中，如果为1，那么引用计数会存储在一个叫SideTable的类的属性中。
* extra_rc：里面存储的值是引用计数器减1后的值。


## Class对象的底层结构

![屏幕快照 2019-05-31 下午4.25.46.png](https://upload-images.jianshu.io/upload_images/4164292-f1d74a6969bad946.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由上图可知，结构体objc_class中的成员变量 **bits & FAST_DATA_MASK** 得到 class_rw_t 的内存地址，进而可以读取其中的方法列表 methods（对象方法或类方法）、属性列表 properties、协议列表 protocols 等信息。结构体 class_rw_t 中的方法列表、属性列表、协议列表都包含当前类以及当前类的分类的方法列表、属性列表、协议列表。

### class_rw _t

结构体 class_rw_t 里面的 methods、properties、protocols 都是二维数组，是可读可写的，包含了类的初始内容和分类的内容。

结构体class_rw_t 底层结构示意图，如下所示：

![class_rw_t 底层结构示意图.png](https://upload-images.jianshu.io/upload_images/4164292-e15b20432cffefc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### class_ro_t

class_rw_t 结构体里面的 class_ro_t *ro 指针指向结构体 class_ro_t。 class_ro_t 里面的 baseMethodList、baseProtocols、ivars、baseProperties 是一维数组，而且是只读的，只包含了类的初始内容。

结构体 class_ro_t 底层结构示意图，如下所示：

![class_ro_t 底层结构示意图.png](https://upload-images.jianshu.io/upload_images/4164292-cf61122383a90122.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### method_t 结构体

method_list_ t 数组里面存放的是 method_t 结构体。method_t 是对方法/函数的封装。

method_t 结构体底层源码如下：

```
struct method_t {
    SEL name;//函数名
    const char *types;//字符串编码，里面存放着返回值类型、参数类型。
    IMP imp;//指向函数的指针（函数地址）

    struct SortBySELAddress :
        public std::binary_function<const method_t&,
                                    const method_t&, bool>
    {
        bool operator() (const method_t& lhs,
                         const method_t& rhs)
        { return lhs.name < rhs.name; }
    };
};
```

**SEL**：代表方法或函数名，一般叫选择器，底层结构跟 char* 类似。

获取 SEL 的方法有两种： 可以通过 @selector() 或者 sel_registerName() 获得。

```
SEL sel1 = sel_registerName("test");
SEL sel2 = @selector(test);

```

此外，可以通过sel_getName() 或者 NSStringFromSelector() 转成字符串。需要注意的是： **不同类中相同名字的方法，所对应的方法选择器是相同的** 。

**name**：表示函数名。

**types**：表示存放着函数的返回值类型、参数类型的字符串编码。比如定义一个 -(void)test方法。

```
-(void)test;
-(void)test:(id)self _cmd:(SEL)_cmd;
// v16@0:8
```

types是“v16@0:8”，其中的“v”表示函数返回值类型void，“@”表示“id”，“:”表示“SEL”，“16”表示整个字符串编码占16个字节，“0”和“8”表示对应的id和SEL分别从第几个字节开始。

**IMP**：代表函数的具体实现。

```
typedef id _Nullable (*IMP)(id _Nonnull, SEL _Nonnull, ...);
```

**imp**：指向函数的指针（函数地址）。


### Type Encoding(类型编码)

iOS中提供了一个叫做@encode的指令，可以将具体的类型表示成字符串编码。比如@encode(id)转化为“@”，@encode(SEL)转化为“:”。

@encode指令表如下图所示：
![TypeEncoding指令表.png](https://upload-images.jianshu.io/upload_images/4164292-079113457883962d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
- (int)test:(int)age height:(float)height;
// 该 OC 方法最终会转化为底层的 C 语言函数，默认传递两个参数：self 和 _cmd。即： 
- (int)test:(id)self _cmd:(SEL)_cmd age:(int)age height:(float)height;
字符串编码 const char *types = "i24@0:8i16f20"; 
/*
i 24 @0 :8 i16 f20
(1)i：返回值类型是int
(2)24：整个字符串占24个字节
(3)@0：第一个参数是 id 类型，且内存地址从第0 bit开始
(4):8：第二个参数是 _cmd，且内存地址从第 8 bit开始
(5)i16：第三个参数是 int 类型，且内存地址从第 16 bit开始
(6)f20：第四个参数是 float 类型，且内存地址从第 20 bit开始
*/
```

### cache_t cache（方法缓存）

![方法缓存.png](https://upload-images.jianshu.io/upload_images/4164292-e00599de0fd69f1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Class内部结构中有一个“方法缓存”，也就是 struct objc_ class中有一个成员变量 cache_ t cache。cache_t cache是用“**散列表**(也称为哈希表)”来缓存曾经调用过的方法的，这样就提高方法的查找速度。

**为什么说使用cache_t方法缓存技术可以提高查找方法的速度呢**？首先我们来回顾一下OC中是如何调用对象方法的。举个例子，比如：

```
HLPerson *person = [[HLPerson alloc]init];
[person test];

```

执行[person test]，其方法调用过程是这样的：

* (1)首先通过实例对象person的isa指针找到其Class对象。
* (2)然后先查看cache_t cache中的缓存方法是否为空（第一次调用的话cache_t cache肯定为空），如果不为空且缓存有将要调用的方法就直接调用；如果为空，则进行步骤（3）。
* (3)通过遍历数组的方式查找Class对象中的对象方法列表method _list _ t *methods，看对象方法列表中是否存在方法名为test的对象方法，如果存在，直接调用并将该方法缓存到Class对象中的cache_t cache里面；如果不存在，那么就通过superclass指针找到其父类的Class对象。
* (4)通过遍历数组的方式查找其父类的Class对象中的对象方法method _list _ t *methods，看是否存在方法名为test的对象方法，如果存在，直接调用并将该方法缓存到Class对象中的 cache_t cache里面；如果不存在，那么就通过superclass指针找到其父类的Class对象....这样一层一层向下查找。

当再次调用test方法时，通过实例对象的isa指针找到其Class对象，然后查找cache_t cache中是否缓存了test方法，如果存在，那么就直接调用cache_t cache中缓存的test方法，不必再一层一层往下查找。这样就提高了方法查找调用的速度。


cache_ t的底层实现如下：

```
struct cache_t {
    struct bucket_t *_buckets;//散列表，是一个数组
    mask_t _mask;//散列表的长度减1
    mask_t _occupied;//已经缓存的方法数量
};
```
_ buckets代表 散列表，是一个数组。 _ mask的值等于_ buckets这个数组的长度减1。_occupied表示已经缓存的方法的数量。

bucket_t 的底层结构如下所示：

```
struct bucket_t {
	cache_key_t _key;//SEL作为key，也就是@selector(methodName)作为key
	IMP _imp;//函数的内存地址
};
```

_ buckets散列表这个数组的每一个元素都是 bucket_t结构体数据，bucket_t中有两个成员变量：_key和 _imp。其中key=@selector(methodName)。

当缓存test方法时，先通过位运算 @selector(methodName) & _mask计算出index，然后将 bucket _ t(_key=@selector(test),_imp)存储在 _ buckets数组中index下标对应的位置。

当查找缓存中的方法时，并不是通过遍历数组这种普通的方式来查找，而是像缓存方法时一样，先通过位运算 @selector(methodName) & _mask计算出index(计算出的index值肯定小于等于 _mask)，然后直接获取index下标对应的元素 bucket _t,并从中获取 _imp,进而直接调用 _imp这个函数地址值对应的函数。由于采取了位运算的方式缓存方法， _buckets这个数组有些元素是NULL，这种“以空间换时间”的方式，虽然牺牲掉了一些内存空间，但是很大程度上提高了执行效率。

【注意】当数组_buckets空间不足时会扩容，扩容时 _mask也会随之变化。那么此时就会将 _buckets数组中的元素全部清空。这一点我们可以从数组_buckets扩容函数expand底层实现源码可以看到。具体源码如下：

```
//expand 扩容函数
void cache_t::expand()
{
    cacheUpdateLock.assertLocked();
    
    uint32_t oldCapacity = capacity();
    uint32_t newCapacity = oldCapacity ? oldCapacity*2 : INIT_CACHE_SIZE;//扩容为原来的2倍

    if ((uint32_t)(mask_t)newCapacity != newCapacity) {
        // mask overflow - can't grow further
        // fixme this wastes one bit of mask
        newCapacity = oldCapacity;
    }

    reallocate(oldCapacity, newCapacity);//调用reallocate函数
}

//reallocate函数实现
void cache_t::reallocate(mask_t oldCapacity, mask_t newCapacity)
{
    bool freeOld = canBeFreed();

    bucket_t *oldBuckets = buckets();
    bucket_t *newBuckets = allocateBuckets(newCapacity);

    // Cache's old contents are not propagated. 
    // This is thought to save cache memory at the cost of extra cache fills.
    // fixme re-measure this

    assert(newCapacity > 0);
    assert((uintptr_t)(mask_t)(newCapacity-1) == newCapacity-1);

    setBucketsAndMask(newBuckets, newCapacity - 1);
    
    if (freeOld) {
        cache_collect_free(oldBuckets, oldCapacity);
        cache_collect(false);
    }
}
```
我们可以看到reallocate函数调用了cache_ collect_free方法将原来的数据清空了，setBucketsAndMask重新设置了 _buckets = newBuckets， _mask = newCapacity - 1。


## objc_msgSend的实现原理

接下来探究objc_msgSend的底层实现。首先创建一个HLPerson类，并声明和实现test方法。然后调用，代码如下：

```
HLPerson *person = [[HLPerson alloc]init];
[person test];
```
将上面的代码转化为C++代码的 Clang 编译器指令：

```
xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc main.m
```

利用Clang编译器指令将上面的代码转化为C++代码，我们可以看到[person test]这行代码转化为:

```
objc_msgSend(person, sel_registerName(@"test"));
```
【注意】sel_registerName(@"test")与@selector(test)完全等价，二者地址打印出来也是相同的。也就是说objc_msgSend(person, sel_registerName(@"test"))完全等价于objc_msgSend(person, @selector(test));

### objc_msgSend执行流程

OC的方法调用是指给方法调用者发送消息，也称为**消息机制**。其中，方法调用方也称为**消息接受者（receiver）**。OC中的方法调用，不管是对象方法还是类方法，本质上都是转换为**objc_msgSend函数的调用**。objc_msgSend的核心源码在objc4-750文件中的objc-runtime-new.mm中的4901行(retry:)~4978行

objc_msgSend函数的执行流程可以分为3大阶段：

* （1）**消息发送阶段**。objc_msgSend(person, @selector(test))，在此阶段会查找test是否存在，如果存在，直接调用，如果不存在，再进行步骤（2）动态方法解析。

![objc_msgSend执行流程-消息发送阶段示意图.png](https://upload-images.jianshu.io/upload_images/4164292-f7eb12d7a09294a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**消息发送流程**：首先判断receiver是否为nil，为nil的话，直接retrun。如果receiver不为nil，那么就利用receiver的isa找到receiver的Class对象或Meta-Class对象。接下来，先从receiver自己的Class的cache中查找方法，如果找到了该方法，直接调用，并结束查找。如果没有找到方法，那么就从receiver的Class对象的class_rw_t中methods数组里查找方法，如果数组已经排序，采用“二分查找”；如果没有排序，则采用普通遍历查找。如果在methods数组里找到了方法，则调用方法，结束查找并将该方法缓存到receiver的Class的cache中；如果没有找到方法，那么就通过receiver Class的superclass指针找到SuperClass，接下来从SuperClass的cache中查找方法，如果找到了方法，则调用该方法，结束查找，并将该方法缓存到receiver的Class对象的cache中；如果没有找到方法，接着会从SuperClass的class _rw _t 中查找方法，同样地，也存在采用“二分查找”和“遍历查找”的判断，如果找到了方法，则调用方法，结束查找并将该方法缓存到receiver的Class的cache中；如果没有找到方法，此时会判断是否存在更高一级的superClass（父类），如果存在superClass，那么继续从superClass的cache中查找方法，继续上面的所述的在superClass中的查找过程；如果上层不存在superClass了，那么此时就会进入**动态方法解析阶段**。

* （2）**动态方法解析阶段**。此阶段允许开发者利用runtime运行时机制动态添加方法实现。如果在动态方法解析阶段，开发者没有执行任何操作，那么将进入“消息转发”阶段。

![objc_msgSend执行流程02-动态方法解析流程示意图.png](https://upload-images.jianshu.io/upload_images/4164292-0b4dc6651b207d00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**动态方法解析流程**：首先判断“是否已经动态解析”(if(resolver && !triedResolver))，如果没有动态解析过，那么就会调用+resolveInstanceMethod:或者+resolveClassMethod:方法来动态解析方法，开发者可以在+resolveInstanceMethod:或者+resolveClassMethod:方法里面利用runtime API来动态添加方法实现；动态解析完成后，会标记为已经动态解析(triedResolver = YES)，重新执行“消息发送”的流程，也就是重新从receiver Class的cache中查找方法。如果一开始判断为经动态解析过，那么将转入“**消息转发阶段**”。需要特别注意的是：如果是为“类方法”动态添加实现的话，class_addMethod的第一个参数必须是Meta-Class对象,也就是objc_getClass(self)。

那么在+resolveInstanceMethod方法中具体如何动态添加方法实现呢？有三种方法：

方法1：推荐使用此方法

```
//动态添加为test方法的实现
- (void)other
{
    NSLog(@"%s",__func__);
}
//调用+resolveInstanceMethod方法进行动态解析方法，可以在此方法中动态添加方法。
+ (BOOL)resolveInstanceMethod:(SEL)sel
{
    if (sel == @selector(test)) {
        //方法1，如果是为“类方法”动态添加实现的话，class_addMethod的第一个参数必须是Meta-Class对象,也就是objc_getClass(self)
        //获取其他方法(比如other方法)：class_getInstanceMethod(Class _Nullable cls, SEL _Nonnull name)
        Method method = class_getInstanceMethod(self, @selector(other));
        //利用runtime为test方法动态添加方法实现（other方法）
        //class_addMethod(Class _Nullable cls, SEL _Nonnull name, IMP _Nonnull imp,const char * _Nullable types)
        class_addMethod(self, sel, method_getImplementation(method), method_getTypeEncoding(method));
        return YES;
    }
    return [super resolveInstanceMethod:sel];
}
```

方法2：利用struct objc_method 与 struct method_t 的等价性

```
//自定义结构体 method_t
struct method_t {
    SEL sel;
    char *types;
    IMP imp;
};
+ (BOOL)resolveInstanceMethod:(SEL)sel
{
    if (sel == @selector(test)) {
        struct method_t *method = (struct objc_method *)class_getInstanceMethod(self, @selector(other));
        class_addMethod(self, sel, method->imp, method->types);
        return YES;
    }
    return [super resolveInstanceMethod:sel];
}

```

方法3：利用C语言函数

```
//C语言函数,将该方法动态添加为test方法的实现
void c_other(id self, SEL _cmd)
{
    NSLog(@"c_other-%@ - %@",self, NSStringFromSelector(_cmd));
}
//调用+resolveInstanceMethod方法进行动态解析方法，可以在此方法中动态添加方法。
+ (BOOL)resolveInstanceMethod:(SEL)sel
{
    if (sel == @selector(test)) {

    //方法3:添加C语言函数，如果是为“类方法”动态添加实现的话，class_addMethod的第一个参数必须是Meta-Class对象,也就是objc_getClass(self)
        class_addMethod(self, sel, (IMP)c_other, "v16@0:8");
        //YES表示已经动态添加方法（其实返回NO效果也一样，为了遵守规范，最好使用YES）
        return YES;
    }
    return [super resolveInstanceMethod:sel];
}
```

* （3）**消息转发阶段**。消息转发是指将方法转发给其他调用者。

![objc_msgSend的执行流程03-消息转发流程示意图.png](https://upload-images.jianshu.io/upload_images/4164292-9b726d453273809c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**消息转发流程**：首先调用forwardingTargetForSelector:方法，如果方法返回值不为nil，那么就执行objc_msgSend(返回值,SEL)，如果返回值为nil，则调用methodSignnatureForSelector:方法，如果返回值为nil，则调用doesNotRecognizeSelector:方法并抛出错误"**unrecognized selector sent to instance**"；如果methodSignnatureForSelector:方法的返回值不为nil，就会调用forwardInvocation:方法，开发者可以在forwardInvocation:方法里自定义任何处理逻辑。

```
//消息转发
- (id)forwardingTargetForSelector:(SEL)aSelector
{
    if (aSelector == @selector(test)) {
        //objc_msgSend(cat,aSelector);
        return [[Cat alloc]init];
        //如果返回nil，则调用methodSignatureForSelector:方法
//        return nil;
    }
    return [super forwardingTargetForSelector:aSelector];
}
//方法签名：返回值类型、参数类型
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector
{
    if (aSelector == @selector(test)) {
        //方法签名,如果返回的方法签名为空，那么将不再调用-(void)forwardInvocation:(NSInvocation *)anInvocation方法，并抛出错误
        return [NSMethodSignature signatureWithObjCTypes:"v16@0:8"];
        //如果返回nil，调用doesNotRecognizeSelector:方法并抛出错误"unrecognized selector sent to instance";如果返回不为nil，则调用forwardInvocation:方法。
//        return nil;
    }
    return [super methodSignatureForSelector:aSelector];
}
//NSInvocation封装了一个方法调用，包括方法调用者、方法名、方法参数
//anInvocation.target 方法调用者
//anInvocation.selector 方法名
//[anInvocation getArgument:NULL atIndex:0];
-(void)forwardInvocation:(NSInvocation *)anInvocation
{
    [anInvocation invokeWithTarget:[[Cat alloc]init]];
}
```

如果以上3个阶段都无法完成消息调用，那么将调用doesNotRecognizeSelector:方法报错"unrecognized selector sent to instance XXX"。

## @dynamic

@dynamic是用来通知编译器不要自动生成getter方法和setter方法的实现(并且不要自动生成成员变量)，等到运行时再添加方法实现的。


<!--### objc_msgSend执行流程 - 源码跟读

搜索“objc_msgSend”，然后找到"objc-msg-arm64.s"文件当中的"ENTRY _objc_msgSend"方法，该方法源码如下：

```
//汇编语言
ENTRY _objc_msgSend
	UNWIND _objc_msgSend, NoFrame
	//x0寄存器，里面存放的是objc_msgSend的第一个参数，也就是消息接收者receiver。
	cmp	p0, #0			// nil check and tagged pointer check
#if SUPPORT_TAGGED_POINTERS
   //b指令表示跳转，le指令表示“<=”，先判断x0是否小于等于0，x0里面存放的是消息接收者receiver，也就是先判断消息接收者是否小于等于0，如果小于等于0，说明是receiver是nil，那么就跳转到LNilOrTagged
	b.le	LNilOrTagged		//  (MSB tagged pointer looks negative)
#else
	b.eq	LReturnZero
#endif
	ldr	p13, [x0]		// p13 = isa
	GetClassFromIsa_p16 p13		// p16 = class
LGetIsaDone:
	//如果receiver != nil，那么会查找缓存CacheLookup
	CacheLookup NORMAL		// calls imp or objc_msgSend_uncached

#if SUPPORT_TAGGED_POINTERS
LNilOrTagged:
	b.eq	LReturnZero		// nil check

	// tagged
	adrp	x10, _objc_debug_taggedpointer_classes@PAGE
	add	x10, x10, _objc_debug_taggedpointer_classes@PAGEOFF
	ubfx	x11, x0, #60, #4
	ldr	x16, [x10, x11, LSL #3]
	adrp	x10, _OBJC_CLASS_$___NSUnrecognizedTaggedPointer@PAGE
	add	x10, x10, _OBJC_CLASS_$___NSUnrecognizedTaggedPointer@PAGEOFF
	cmp	x10, x16
	b.ne	LGetIsaDone

	// ext tagged
	adrp	x10, _objc_debug_taggedpointer_ext_classes@PAGE
	add	x10, x10, _objc_debug_taggedpointer_ext_classes@PAGEOFF
	ubfx	x11, x0, #52, #8
	ldr	x16, [x10, x11, LSL #3]
	b	LGetIsaDone
// SUPPORT_TAGGED_POINTERS
#endif

LReturnZero:
	// x0 is already zero
	mov	x1, #0
	movi	d0, #0
	movi	d1, #0
	movi	d2, #0
	movi	d3, #0
	//汇编中的ret相当于C语言中的return
	ret

	END_ENTRY _objc_msgSend
```-->

## 
