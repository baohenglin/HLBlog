# iOS之Runtime原理探究


Objective-C语言是一门动态性比较强的语言，与C、C++有很大不同。OC语言可以做到在程序运行时动态修改之前编译好的代码逻辑。也可以动态地添加某些方法的实现。

Objective-C的动态性是由Runtime API来支撑和实现的。Runtime即运行时，它提供了一套C语言的API。Rumtime的源码是开源的，Runtime的源码是基于C/C++/汇编语言编写的。

Apple源码下载地址：<https://opensource.apple.com/tarballs/objc4/>

## isa详解-位域

学习Runtime之前，我们需要了解它底层涉及的一些知识，比如isa指针。在arm64架构之前，isa就是一个普通的指针，存放着Class对象或者Meta-Class对象的内存地址。但是从arm64架构开始，苹果对isa进行了优化，采用了一个共用体(union)结构，还使用了“位域”使一个64bit的内存空间存储了更多的信息，其中33bit用来存储Class对象或Meta-Class对象的内存地址。这也就是从arm64开始isa指针只有"&ISA_MASK"才能得到Class对象或Meta-Class对象的地址值的原因(利用位运算)。


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
* **shiftcls**：存储着Class、Meta-Class对象的内存地址信息，最后面的3bit一定是0。
* magic：用于在调试时分辨对象是否未完成初始化。
* weakly_referenced：是否曾经被弱引用指向过。如果没有，释放时会更快。
* deallocating：标记对象是否正在释放。
* has_sidetable_rc：引用计数器是否过大无法存储在isa中，如果为1，那么引用计数会存储在一个叫SideTable的类的属性中。
* extra_rc：里面存储的值是引用计数器减1后的值。


## Class对象的底层结构

![屏幕快照 2019-05-31 下午4.25.46.png](https://upload-images.jianshu.io/upload_images/4164292-f1d74a6969bad946.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由上图可知，结构体objc_class中的成员变量bits &FAST_DATA_MASK得到class_rw_t的地址，进而可以读取其中的方法列表methods（对象方法或类方法）、属性列表properties、协议列表protocols等信息。结构体class_rw_t中的方法列表、属性列表、协议列表都包含当前类以及当前类的分类的方法列表、属性列表、协议列表。

### class_rw _t

结构体class_rw _t里面的methods、properties、protocols都是是二维数组，是可读可写的，包含了类的初始内容和分类的内容。

结构体class_rw_t底层结构示意图，如下所示：

![class_rw_t底层结构示意图.png](https://upload-images.jianshu.io/upload_images/4164292-e15b20432cffefc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### class_ro _t

class_rw _ t 结构体里面的 class_ro _ t *ro 指针指向结构体class_ro _t。 class _ ro _t 里面的baseMethodList、baseProtocols、ivars、baseProperties是一维数组，而且是只读的，只包含了类的初始内容。

结构体class_ro _t 底层结构示意图，如下所示：

![class_ro_t底层结构示意图.png](https://upload-images.jianshu.io/upload_images/4164292-cf61122383a90122.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### method_t结构体

methodList数组里面存放的是method_t结构体。method_t是对方法/函数的封装。

method_t结构体底层源码如下：

```
struct method_t {
    SEL name;//函数名
    const char *types;//字符串编码，里面存放着返回值类型、参数类型。
    MethodListIMP imp;//指向函数的指针（函数地址）

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

SEL：代表方法或函数名，一般叫选择器，底层结构跟char*类似。SEL可以通过@selector()或者sel_registerName()获得。可以通过sel_getName()或者NSStringFromSelector()转成字符串。需要注意的是： **不同类中相同名字的方法，所对应的方法选择器是相同的** 。

name：表示函数名。

types：表示存放着函数的返回值类型、参数类型的字符串编码。比如定义一个 -(void)test方法，types是“v16@0:8”，其中的“v”表示函数返回值类型void，“@”表示“id”，“:”表示“SEL”，“16”表示整个字符串编码占16个字节，“0”和“8”表示对应的id和SEL分别从第几个字节开始。

IMP：代表函数的具体实现。

imp：是指向函数的指针（函数地址）。


### Type Encoding(类型编码)

iOS中提供了一个叫做@encode的指令，可以将具体的类型表示成字符串编码。比如@encode(id)转化为“@”，@encode(SEL)转化为“:”。

@encode指令表如下图所示：
![TypeEncoding指令表.png](https://upload-images.jianshu.io/upload_images/4164292-079113457883962d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### cache_t cache（方法缓存）

Class内部结构中有一个“方法缓存”，也就是 struct objc_ class中有一个成员变量 cache_ t cache。cache_t cache是用“**散列表**(也称为哈希表)”来缓存曾经调用过的方法的，这样就提高方法的查找速度。

为什么说使用cache_t方法缓存技术可以提高查找方法的速度呢？首先我们来回顾一下OC中是如何调用对象方法的。举个例子，比如：

```
HLPerson *person = [[HLPerson alloc]init];
[person test];

```

第一次执行[person test]，其方法调用过程是这样的：

* (1)首先通过实例对象person的isa指针找到其Class对象。
* (2)通过遍历数组的方式查找Class对象中的对象方法列表method _list _ t *methods，看对象方法列表中是否存在方法名为test的对象方法，如果存在，直接调用并将该方法缓存到Class对象中的cache_t cache里面；如果不存在，那么就通过superclass指针找到其父类的Class对象。
* (3)通过遍历数组的方式查找其父类的Class对象中的对象方法method _list _ t *methods，看是否存在方法名为test的对象方法，如果存在，直接调用并将该方法缓存到Class对象中的 cache_t cache里面；如果不存在，那么就通过superclass指针找到其父类的Class对象....这样一层一层向下查找

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



## Runtime相关知识

（1）简述一下OC的消息机制

（2）消息转发机制的流程

（3）什么是Runtime？平时项目中用过么？
