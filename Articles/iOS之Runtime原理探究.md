# iOS之Runtime原理探究


Objective-C语言是一门动态性比较强的语言，与C、C++有很大不同。OC语言可以做到在程序运行时动态修改之前编译好的代码逻辑。也可以动态地添加某些方法的实现。

Objective-C的动态性是由Runtime API来支撑和实现的。Runtime即运行时，它提供了一套C语言的API。Rumtime的源码是开源的，Runtime的源码是基于C/C++/汇编语言编写的。

Apple源码下载地址：<https://opensource.apple.com/tarballs/objc4/>

## isa详解

学习Runtime之前，我们需要了解它底层涉及的一些知识，比如isa指针。在arm64架构之前，isa就是一个普通的指针，存放着Class对象或者Meta-Class对象的内存地址。但是从arm64架构开始，苹果对isa进行了优化，采用了一个共用体(union)结构，还使用了“位域”使一个64bit的内存空间存储了更多的信息，其中44bit用来存储Class对象或Meta-Class对象的内存地址。这也就是从arm64开始isa指针只有&ISA_MASK才能得到Class对象或Meta-Class对象的地址值的原因。


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

宏定义“#define ISA_BITFIELD”的源码如下：

```
//结构体支持“位域”。 
#define ISA_BITFIELD                                                        
      uintptr_t nonpointer        : 1; //表示nonpointer在内存中占1bit                                      
      uintptr_t has_assoc         : 1;                                         
      uintptr_t has_cxx_dtor      : 1;                                         
      uintptr_t shiftcls          : 44; /*MACH_VM_MAX_ADDRESS 0x7fffffe00000*/ \
      uintptr_t magic             : 6;//magic在内存中占6bit                                         
      uintptr_t weakly_referenced : 1;                                         
      uintptr_t deallocating      : 1;                                         
      uintptr_t has_sidetable_rc  : 1;                                         
      uintptr_t extra_rc          : 8
```

共用体的概念：所有成员变量共用同一块内存。

结构体ISA_BITFIELD中各个参数的含义如下：

* nonpointer：0代表普通的指针，存储着Class、Meta-Class对象的内存地址；1代表优化过，使用位域存储更多信息。
* has_assoc：是否设置过关联对象，如果没有，释放时会更快。
* has_cxx_dtor：是否有C++的析构函数（.cxx_destruct），如果没有，释放时会更快。
* **shiftcls**：存储着Class、Meta-Class对象的内存地址信息，最后面的3bit一定是0。
* magic：用于在调试时分辨对象是否未完成初始化。
* weakly_referenced：是否有被弱引用指向过，如果没有，释放时会更快。
* deallocating：对象是否正在释放。
* has_sidetable_rc：引用计数器是否过大无法存储在isa中，如果为1，那么引用计数会存储在一个叫SideTable的类的属性中。
* extra_rc：里面存储的值是引用计数器减1后的值。



## Runtime相关知识

（1）简述一下OC的消息机制

（2）消息转发机制的流程

（3）什么是Runtime？平时项目中用过么？