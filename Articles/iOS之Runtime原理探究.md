# iOS之Runtime原理探究


Objective-C语言是一门动态性比较强的语言，与C、C++有很大不同。OC语言可以做到在程序运行时动态修改之前编译好的代码逻辑。也可以动态地添加某些方法的实现。

Objective-C的动态性是由Runtime API来支撑和实现的。Runtime即运行时，它提供了一套C语言的API。Rumtime的源码是开源的，Runtime的源码是基于C/C++/汇编语言编写的。

Apple源码下载地址：<https://opensource.apple.com/tarballs/objc4/>

## isa详解

学习Runtime之前，我们需要了解它底层涉及的一些知识，比如isa指针。在arm64架构之前，isa就是一个普通的指针，存放着Class对象或者Meta-Class对象的内存地址。从arm64架构开始，苹果对isa进行了优化，变成了一个共用体(union)结构，还使用了“位域”来存储更多的信息。

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






## Runtime相关知识

（1）简述一下OC的消息机制

（2）消息转发机制的流程

（3）什么是Runtime？平时项目中用过么？