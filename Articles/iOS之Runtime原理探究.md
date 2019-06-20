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


## Runtime相关知识

（1）简述一下OC的消息机制

（2）消息转发机制的流程

（3）什么是Runtime？平时项目中用过么？
