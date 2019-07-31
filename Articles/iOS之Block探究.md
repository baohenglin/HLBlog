iOS之Block探究篇

## block的本质

block本质上是一个OC对象,因为它内部有一个isa指针。更确切地说，block是封装了函数调用以及函数调用环境的OC的对象。

下面我们从实际代码出发一步一步来探究block的本质。首先我们在main.m中定义一个block。代码如下：

```
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        int age = 10;
        //定义block
        void(^HLblock)(void) = ^(){
            NSLog(@"age is %d",age);
            //age is 10
        };
        
        age = 20;
        //调用block
        HLblock();
    }
    return 0;
}
```

然后利用Clang编译器终端命令将OC代码转化为底层的C/C++代码。Clang终端命令如下：

```
//此命令行生成名为main-arm64.cpp的文件，代码大概有3万多行，推荐使用此终端命令。
xcrun  -sdk iphoneos12.1  clang  -arch  arm64  -rewrite-objc  main.m  -o  main-arm64.cpp
```
或者使用这行终端命令：

```
//此命令生成默认文件名为main.cpp的文件，生成的代码大概有10万行。
clang -rewrite-objc main.m
```

从生成的main-arm64.cpp文件中，我们可以看到主要代码如下：

```
struct __main_block_impl_0 {
  struct __block_impl impl;
  struct __main_block_desc_0* Desc;
  int age;
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, int _age, int flags=0) : age(_age) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};

struct __block_impl {
  void *isa;
  int Flags;
  int Reserved;
  void *FuncPtr;
};

static struct __main_block_desc_0 {
  size_t reserved;
  size_t Block_size;
} __main_block_desc_0_DATA = { 0, sizeof(struct __main_block_impl_0)};


static void __main_block_func_0(struct __main_block_impl_0 *__cself) {
  //__main_block_func_0函数 封装了block执行逻辑的函数
  int age = __cself->age; // bound by copy

            NSLog((NSString *)&__NSConstantStringImpl__var_folders_5__mtg9xgxd1d38kgcthl4yryb80000gn_T_main_552ec3_mi_0,age);
       
}

int main(int argc, const char * argv[]) {
    /* @autoreleasepool */ { __AtAutoreleasePool __autoreleasepool; 
        int age = 10;
        void(*HLblock)(void) = ((void (*)())&__main_block_impl_0((void *)__main_block_func_0, &__main_block_desc_0_DATA, age));
        age = 20;
        ((void (*)(__block_impl *))((__block_impl *)HLblock)->FuncPtr)((__block_impl *)HLblock);
    }
    return 0;
}
```

其中，main函数对应的代码如下：

```
int main(int argc, const char * argv[]) {
    /* @autoreleasepool */ { __AtAutoreleasePool __autoreleasepool; 
        int age = 10;
        void(*HLblock)(void) = ((void (*)())&__main_block_impl_0((void *)__main_block_func_0, &__main_block_desc_0_DATA, age));
        age = 20;
        ((void (*)(__block_impl *))((__block_impl *)HLblock)->FuncPtr)((__block_impl *)HLblock);
    }
    return 0;
}
```

为了看起来更方便，我们可以把相关的类型强转删掉，比如“(void (*)(__block_impl *)”，删除后代码如下：

```
int main(int argc, const char * argv[]) {
    /* @autoreleasepool */ { __AtAutoreleasePool __autoreleasepool; 
        int age = 10;
        void(*HLblock)(void) = &__main_block_impl_0(__main_block_func_0, &__main_block_desc_0_DATA, age);
        age = 20;
        HLblock->FuncPtr(HLblock);
    }
    return 0;
}
```

从简化后的代码可知，block“=”左边代码如下：

```
void(*HLblock)(void)
```
这是一个无参无返回值的函数指针。该函数指针变量里保存着函数地址。block“=”右边代码如下：

```
&__main_block_impl_0(__main_block_func_0, &__main_block_desc_0_DATA, age);
```

"&"(取址符)表示取址。“&”右面是__main_block_impl_0()函数调用， __main_block_impl_0函数有三个参数。__main_block_impl_0的底层实现是一个结构体。该结构体如下：

```
struct __main_block_impl_0 {
  struct __block_impl impl;
  struct __main_block_desc_0* Desc;
  int age;
  //C++构造函数(类似于OC的init方法),返回结构体对象
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, int _age, int flags=0) : age(_age) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
```
其实，C++中的结构体相当于Objective-C的类（Class）。C++的结构体定义了成员变量、构造函数等。block等号右边的“&__main_block_impl_0”其实取的是下面这段代码的地址：

```
__main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, int _age, int flags=0) : age(_age) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
```

上面这个方法实现里有4个参数，而刚才调用的时候明明只有3个参数，我们看到多出了一个参数flag=0，这个参数是默认值，不传也存在值，可以忽略。

* age(_age) 在C++语法中，表示将形参 _age 赋值给实参age，也就是赋值给struct __main_block_impl_0中的成员变量int age，此时int age = 10。
* impl.FuncPtr = fp;表示将形参fp赋值给struct __block_impl impl中的FuncPtr。这里的fp参数里存放的是下面这段代码的函数地址：

```
static void __main_block_func_0(struct __main_block_impl_0 *__cself) {
  //__main_block_func_0函数 封装了block执行逻辑的函数
  int age = __cself->age; // bound by copy
    NSLog((NSString *)&__NSConstantStringImpl__var_folders_5__mtg9xgxd1d38kgcthl4yryb80000gn_T_main_552ec3_mi_0,age);   
}
```
* __main_block_desc_0_DATA，也是一个结构体，里面定义了有关block内存大小的信息。具体实现如下：

```
static struct __main_block_desc_0 {
  size_t reserved;
  size_t Block_size;
} __main_block_desc_0_DATA = { 0, sizeof(struct __main_block_impl_0)};
```

* int _age参数, 也就是构造函数 __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, int _age, int flags=0) : age(_age) {}中的参数 int _age，这里_age是具体值，而不是地址，也就是说这里用到的是“值传递”，不是“地址传递”。
* HLblock->FuncPtr(HLblock); 调用block，从block中获取FuncPtr，再利用FuncPtr找到构造函数__main_block_func_0并调用（将HLblock作为参数传递给__main_block_func_0）。也就是说这里的FuncPtr存放的是下面这段代码的函数地址：

```
static void __main_block_func_0(struct __main_block_impl_0 *__cself) {
  //__main_block_func_0函数 封装了block执行逻辑的函数
  int age = __cself->age; //读取结构体__main_block_impl_0的成员变量age的值并赋值给局部变量age
    NSLog((NSString *)&__NSConstantStringImpl__var_folders_5__mtg9xgxd1d38kgcthl4yryb80000gn_T_main_552ec3_mi_0,age);   
}
```


block底层实现逻辑示意图如下：

![block底层实现逻辑图](https://upload-images.jianshu.io/upload_images/4164292-57baf7f0cff2d3d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'block底层实现逻辑图')

综上所述，block经过编译后生成了一个__main_block_impl_0类型的结构体，该结构体内部有一个__block_impl结构体类型的成员变量，并且__block_impl内部有一个isa指针，这就说明block本质上是一个OC对象。

block在内存中的布局，如下图所示：

![block在内存中的布局.png](https://upload-images.jianshu.io/upload_images/4164292-7045c4c2c191266a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## block的变量捕获（capture）

为了保证block内部能够正常访问外部的变量，block有一个变量捕获机制。什么是block的变量捕获机制呢？block的变量捕获机制是指block底层的结构体内部会专门新增一个成员变量用于存储捕获到的外部值。block的变量捕获分为以下几种：

![block变量捕获的几种类型](https://upload-images.jianshu.io/upload_images/4164292-c4d296b626735bf4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'block变量捕获的几种类型')

* auto修饰符：auto修饰的变量称为自动变量，也就是说auto变量离开作用于就立即销毁。默认情况下，默认使用auto修饰。访问方式是“值传递”。
* static修饰符：使用static修饰的变量一直会存储在内存中，访问方式是“地址传递”。

只要是局部变量，不管是auto修饰的还是static修饰的局部变量，block都可以成功捕获。而全局变量不会被捕获到block内部(因为全局变量都可以访问，无须捕获到block内部),是直接访问。

## block的类型

block有3种类型。可以通过调用class方法或者isa指针查看具体类型，但是无论是3种类型中的哪种类型，都是继承自NSBlock类型的。block的3种类型分别为：

* __NSGlobalBlock__(_NSConcreteGlobalBlock)
* __NSStackBlock__(_NSConcreteStackBlock)
* __NSMallocBlock__(_NSConcreteMallocBlock)

![应用程序的内存分布示意图.png](https://upload-images.jianshu.io/upload_images/4164292-2a6cedb6f04ba8da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* BSS段：用来存储未初始化的全局变量、静态变量。一旦全局变量或静态变量被初始化就会被回收，并转存到数据段中。
* .text区：也称代码段，主要是用来存放代码的，程序结束时系统会自动回收存储在代码段中的数据，内存区域较小。
* .data区：也称数据段，用来存储 已经初始化的全局变量、静态变量，直到程序结束时才会被收回。
* 堆：用来动态分配内存（需要程序员申请内存，也需要程序员自己管理内存），比如alloc/malloc出来的对象一般都存储在堆段。
* 栈：用来存放局部变量（auto变量），特点是系统会自动分配内存，并且在超出其作用域时系统会自动销毁该变量的内存。


## 3种类型的block是如何产生的？

那么这3种类型的block都是怎样产生的呢？总结如下：

![3种block产生场景示意图.png](https://upload-images.jianshu.io/upload_images/4164292-b9bd8134e611fe92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* (1)没有访问auto变量产生的是__NSGlobalBlock__。

```
void(^HLBlock1)(void) = ^{
    NSLog(@"Hello world");
};
NSLog(@"[HLBlock1 class] = %@",[HLBlock1 class]);
```

打印如下：

```
[HLBlock1 class] = __NSGlobalBlock__
```

【总结】访问static变量或者全局变量 生成的是__NSGlobalBlock__。

* (2)访问auto变量，在关闭ARC的情况下，生成的是__NSStackBlock。__NSStackBlock存在一个问题，因为超出作用于后变量已经被系统自动销毁，此时再访问该变量存在安全问题。如果开启ARC，编译器会自动进行copy操作，将__NSStackBlock转变为__NSMallocBlock。
* (3) __NSStackBlock__调用copy方法，就会将block内存搬到堆上，变成了__NSMallocBlock。


三种类型的block分别调用copy方法后产生的结果如下图所示：

![block调用copy方法.png](https://upload-images.jianshu.io/upload_images/4164292-4bc44e72a156c286.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## ARC环境下 block的copy操作

在ARC环境下，编译器会根据情况自动将栈上的block复制(copy)到堆上。比如以下情况：

* (1)block作为函数返回值时

```
typedef void(^HLBlock) (void);
//test方法
HLBlock test()
{
    //定义block,
    int age = 1111;
    HLBlock block = ^{
        //block访问了auto变量age，是__NSStackBlock__类型的block，存储在栈上
        NSLog(@"block作为函数的参数返回值的情况---%d",age);
    };
    
    //block作为函数的返回值
    return block;
    //在MRC环境下，由于block访问了auto变量age，存储在栈上，当超出test函数的作用域时，block在栈上的内存会被系统自动回收。那么在test函数外再调用block时就会出问题。这种情况下，就需要手动进行一次copy操作。
    //在ARC环境下，编译器会自动执行copy操作，将栈上的block拷贝到堆上，由__NSStackBlock__变为__NSMallocBlock__。
    //return [block copy];
  
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        HLBlock block = test();
        //调用block
        block();
        NSLog(@"----%@",[block class]);
        //2019-06-17 10:50:31.038489+0800 Block的copy操作[1003:37627] ----__NSMallocBlock__

    }
    return 0;
}
```
* (2)将block赋值给__strong指针时

未将block赋值给强指针的情况，在ARC环境下编译器不会自动执行copy操作。代码如下：

```
int main(int argc, const char * argv[]) {
    @autoreleasepool {        
        int age = 1110;
        NSLog(@"----%@",[^{
            //由于block访问了auto变量，因此是__NSStaticBlock__类型
            NSLog(@"block：age---%d",age);
            //2019-06-17 11:13:06.358166+0800 Block的copy操作[1139:44714] ----__NSStackBlock__
            //由以上打印结果可知：没有将block赋值给强指针时，在ARC环境下编译器不会自动执行copy操作
        } class]);
    }
    return 0;
}
```

将block赋值给强指针的情况，在ARC环境下，编译器对block自动进行一次copy操作[block copy]，block的类型由__NSStaticBlock__变为了__NSMallocBlock__。代码如下：

```
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        int age = 1110;
        //将block赋值给强指针的情况
        HLBlock block = ^{
            //由于block访问了auto变量，因此是__NSStaticBlock__类型
            NSLog(@"block：age---%d",age);
        };
        block();
        NSLog(@"----%@",[block class]);
        //2019-06-17 11:01:11.834323+0800 Block的copy操作[1058:40766] ----__NSMallocBlock__
       //由以上打印结果可知：将block赋值给强指针时，在ARC环境下，编译器对block自动进行一次copy操作[block copy]，block的类型由__NSStaticBlock__变为了__NSMallocBlock__。

    }
    return 0;
}
```

* (3)block作为Cocoa的API中方法名含有usingBlock的方法的参数时

```
NSArray *arr1 = @[@"100",@"20",@"31",@"42",@"56"];
[arr1 enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
    NSLog(@"obj=%@-------idx=%lu",obj,(unsigned long)idx);
}];
```
* (4)block作为GCD API的方法参数时。比如：

```
static dispatch_once_t onceToken;
dispatch_once(&onceToken, ^{
    
});
```
再比如：

```
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            
});
```

block属性的写法：

（1）MRC环境下

```
@property (nonatomic, copy) void(^block)(void);

```

（2）ARC环境下

```
//这两种写法都可以，没有区别（因为只要是对block进行强引用，就会自动对block进行copy）
@property (nonatomic, strong) void(^block)(void);
@property (nonatomic, copy) void(^block)(void);

```

不管MRC还是ARC环境，建议统一都使用“copy”来修饰block。


## block访问对象类型的auto变量

当block内部访问了对象类型的auto变量时，如果block是在栈上（也就是_NSConcreteStackBlock类型的block），那么将不会对auto变量产生强引用。

当block被拷贝到堆上时，那么会自动调用block内部的copy函数(__main_block_copy_0函数)，__main_block_copy_0函数内部会调用_Block_object_assign函数，然后_Block_object_assign函数会根据auto变量的修饰符(__strong、__weak、__unsafe_unretained)做出相应的操作，类似于retain（形成强引用或者弱引用）。

当block从堆中被移除时，会调用block内部的dispose函数(__main_block_dispose_0函数)，__main_block_dispose_0函数内部会调用_Block_object_dispose函数，_Block_object_dispose函数会自动释放引用的auto变量类似于release操作。

## 被__block修饰符修饰的基本类型

默认情况下，block不能直接修改block外部的局部变量(auto变量)。那么怎么才能修改呢？有以下几种方案：

* （1）使用static修饰符修饰局部变量（不推荐）
* （2）将局部变量修改为全局变量（不推荐）
* （3）使用__block修饰符（推荐）

代码如下：

```
typedef void(^HLBlock) (void);
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        __block int age = 11;
        HLBlock block = ^{
            age = 12;
            NSLog(@"age is %d",age);
        };
        block();
    }
    return 0;
}
```

打印结果如下：

```
2019-06-17 15:41:47.716010+0800 Block之__block修饰符[2641:125625] age is 12

```

__block可以用于解决block内部无法修改外部的auto变量值得问题。__block不能修饰全局变量，也不能修饰静态变量(static变量)。使用__block修饰符不会修改变量的性质，变量还是自动变量。所以我们推荐使用__block来修改auto变量值（因为前两种方法会使变量一直占用内存，不会释放内存）


将上面代码转化为C++代码，如下：

```
struct __main_block_impl_0 {
  struct __block_impl impl;
  struct __main_block_desc_0* Desc;
  __Block_byref_age_0 *age; // by ref
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, __Block_byref_age_0 *_age, int flags=0) : age(_age->__forwarding) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};

//__Block_byref_age_0结构体
struct __Block_byref_age_0 {
  void *__isa;
__Block_byref_age_0 *__forwarding;
 int __flags;
 int __size;
 int age;//age=11
};
```

当使用__block修饰auto变量时，编译器会将__block变量封装成一个对象__Block_byref_age_0。该对象的成员变量包括__isa指针、__forwarding指针、__flags、__size以及age。其中__forwarding指针指向自身。



## __block的内存管理

* 当block在栈上（也就是_NSConcreteStackBlock类型的block）时，那么将不会对__block变量产生强引用。

* 当block被拷贝到堆时，那么会自动调用block内部的copy函数(__main_block_copy_0函数)，__main_block_copy_0函数内部会调用_Block_object_assign函数，然后_Block_object_assign函数会对__block变量形成强引用(retain)。

__block的内存管理示意图，如下所示：

![__block的内存管理示意图.png](https://upload-images.jianshu.io/upload_images/4164292-8c36a6f3228238f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


假设有两个block：block0和block1，而且这两个block都访问了__block变量。一开始这两个block都在栈上，__block变量也在栈上。当block0被拷贝到堆上时，会自动将block内部使用到的__block变量也拷贝到堆上，而且被拷贝到堆上的block0对被拷贝到栈上的__block变量是强引用；block1也会被拷贝到堆上面，由于之前已经将__block变量拷贝到堆上了，所以这一次不再拷贝，而且被拷贝到堆上的block1堆__block变量也是强引用。

* 当block从堆中被移除时，会调用block内部的dispose函数(__main_block_dispose_0函数)，__main_block_dispose_0函数内部会调用_Block_object_dispose函数，_Block_object_dispose函数会自动释放引用的__block变量,类似于release操作。

![block移除时的内存管理.png](https://upload-images.jianshu.io/upload_images/4164292-3196af94ae66cdd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 对象类型的auto变量和__block变量的异同

相同点如下：

* 当block在栈上时，对它们都不会产生强引用；
* 当block拷贝到堆上时，都会通过copy函数来处理它们；
* 当block从堆上移除时，都会通过dispose函数来释放它们。

不同点：
不同点主要在于引用方面（强引用还是弱引用）。

* 对于OC对象类型的auto变量来说，如果block是通过弱引用来访问OC对象的话，那么block对OC对象产生的弱引用；如果block是通过强引用来访问OC对象的话，那么block对OC对象产生的是强引用。
* 对于__block变量来说，block对__block变量直接产生的就是强引用。


## __block 的 __forwarding 指针


通过一个例子来说明，代码如下：

```
typedef void(^HLBlock) (void);
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        __block int age = 11;
        HLBlock block = ^{
            age = 12;
            NSLog(@"age is %d",age);
        };
        block();
    }
    return 0;
}
```

利用Clang编译器将上述代码转化为C++代码，代码如下：

```
struct __Block_byref_age_0 {
  void *__isa;
__Block_byref_age_0 *__forwarding;
 int __flags;
 int __size;
 int age;
};

struct __main_block_impl_0 {
  struct __block_impl impl;
  struct __main_block_desc_0* Desc;
  __Block_byref_age_0 *age; // by ref
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, __Block_byref_age_0 *_age, int flags=0) : age(_age->__forwarding) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
static void __main_block_func_0(struct __main_block_impl_0 *__cself) {
  __Block_byref_age_0 *age = __cself->age; // bound by ref

    (age->__forwarding->age) = 12;
    NSLog((NSString *)&__NSConstantStringImpl__var_folders_5__mtg9xgxd1d38kgcthl4yryb80000gn_T_main_c9cab7_mi_0,(age->__forwarding->age));
}
static void __main_block_copy_0(struct __main_block_impl_0*dst, struct __main_block_impl_0*src) {_Block_object_assign((void*)&dst->age, (void*)src->age, 8/*BLOCK_FIELD_IS_BYREF*/);}

static void __main_block_dispose_0(struct __main_block_impl_0*src) {_Block_object_dispose((void*)src->age, 8/*BLOCK_FIELD_IS_BYREF*/);}

static struct __main_block_desc_0 {
  size_t reserved;
  size_t Block_size;
  void (*copy)(struct __main_block_impl_0*, struct __main_block_impl_0*);
  void (*dispose)(struct __main_block_impl_0*);
} __main_block_desc_0_DATA = { 0, sizeof(struct __main_block_impl_0), __main_block_copy_0, __main_block_dispose_0};
int main(int argc, const char * argv[]) {
    /* @autoreleasepool */ { __AtAutoreleasePool __autoreleasepool; 
        __attribute__((__blocks__(byref))) __Block_byref_age_0 age = {(void*)0,(__Block_byref_age_0 *)&age, 0, sizeof(__Block_byref_age_0), 11};
        HLBlock block = ((void (*)())&__main_block_impl_0((void *)__main_block_func_0, &__main_block_desc_0_DATA, (__Block_byref_age_0 *)&age, 570425344));
        ((void (*)(__block_impl *))((__block_impl *)block)->FuncPtr)((__block_impl *)block);
    }
    return 0;
}
```
其中，构造函数__main_block_func_0中有下列赋值代码：

```
(age->__forwarding->age) = 12;
```

赋值过程是这样的：首先利用结构体__Block_byref_age_0，查找到里面的__forwarding成员变量，对于栈上的block，__forwarding指向自身，也就是指向__Block_byref_age_0，之后通过__forwarding找到自身(__Block_byref_age_0)，最后找到里面的age成员变量进行赋值。那么为什么要先利用__forwarding指针查找一次自己呢？这是为了保证拷贝到堆上的block访问外部的__block变量时，被访问的__block变量也必须存放在堆空间。


![__block的__forwarding指针.png](https://upload-images.jianshu.io/upload_images/4164292-afb2a15da907e116.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图可知，对于block从栈copy到堆的情况，结构体__Block_byref_age_0中的__forwarding的指针不再指向自身，而是指向copy到堆空间的__Block_byref_age_0，堆上的__Block_byref_age_0的__forwarding指针指向自身，此时执行(age->__forwarding->age) = 12赋值操作时，改变的age变量就是存储在堆空间的age变量了，而不是栈中的age变量。


<!--## 被__block修饰的对象类型-->

## 循环引用

导致循环引用的原因是Block通过内部的__strong self指针持有某对象，同时该对象通过其成员变量_block持有该Block，从而使对象和Block二者相互强引用，都不能被释放。

![循环引用示意图](https://upload-images.jianshu.io/upload_images/4164292-d6c1da4d9c02faf5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 '循环引用示意图')

### ARC环境下的循环引用解决方法

ARC环境下解决循环引用的方法有以下3种：

* __ unsafe_unretained

```
//方法(1)
__unsafe_unretained typeof(self) weakSelf = self;
self.block = ^{
	NSLog(@"%p", weakSelf);
};
```

* __block

```
//方法(2)
__block id weakSelf = self;
self.block = ^{
	NSLog(@"%p",weakSelf);
	weakSelf = nil;//必须写
};
self.block();//必须写
```

* __weak

```
//方法(3)
__weak typeof(self) weakSelf = self;
self.block = ^{
	NSLog(@"%p", weakSelf);
};

```

ARC环境下，可以通过 __ unsafe_unretained 修饰符来解决，但是由于__ unsafe_unretained是不安全的，当指针指向的对象销毁时，指针存储的地址值不变，也就是不会自动将指针置为nil，从而产生野指针；所以不推荐使用 __unsafe_unretained。

ARC环境下，也可以通过__block来解决循环引用。缺点是必须要调用block，而且在block内部要将指向对象的指针置为nil。


ARC环境下，可以通过 __weak 修饰符来解决循环引用。__weak是安全的，当指针指向的对象销毁时，会自动将指针置为nil。

综上所述，优先推荐使用__weak修饰符来解决循环引用问题。


### ARC环境下 __ unsafe_unretained和__weak的异同点

相同点：__ unsafe_unretained和__weak都是弱引用，不会产生强引用。

不同点：__ unsafe_unretained是不安全的，当指针指向的对象销毁时，指针存储的地址值不变，也就是不会自动将指针置为nil，从而产生野指针；__weak是安全的，当指针指向的对象销毁时，会自动将指针置为nil。


### MRC环境下的循环引用解决方法

MRC环境下，不支持__weak，不能通过__weak来解决循环引用。但是可以通过 __ unsafe_unretained和__block来解决MRC环境下的循环引用问题。

* __ unsafe_unretained

```
__unsafe_unretained typeof(self) weakSelf = self;
self.block = ^{
	NSLog(@"%p", weakSelf);
};
```

* __block: MRC环境下， __block修饰的对象，不会被 __block变量的结构体对象强引用，也就打破了循环引用。

```
__block id weakSelf = self;
self.block = ^{
	NSLog(@"%p",weakSelf);
};

```

## block知识总结
(1)block的本质是什么?底层原理是怎样的?

&emsp;&emsp;block本质上是一个OC对象,因为它内部有一个isa指针。更确切地说，block是封装了函数调用以及函数调用环境的OC的对象。

(2)__block的作用是什么?使用时需要注意什么?

&emsp;&emsp; __block可以用于解决block内部无法修改auto变量值的问题。一旦使用 __block，那么编译器会将 __block变量包装成一个对象 ( __Block _byref _变量名 _0)。该对象内部包含isa指针以及与外部auto变量同名且同类型的成员变量。

使用注意点：注意 __ block的内存管理问题；再就是在MRC环境下，使用__block修饰的对象类型不会被block强引用。

(3)block的属性修饰词为什么是copy？使用block有哪些注意事项？

&emsp;&emsp;block如果不执行copy操作，就不会被拷贝到堆上，通过copy操作来保证block在堆上的目的是开发者可以控制block的生命周期，并对该block进行内存管理。

注意事项：循环引用问题。

(4)block在修改NSMutableArray时，需不需要加__block？

不需要加__block。

(5)__weak只能用来修饰对象类型的变量，不能修饰基本数据类型的变量。

<br>
<br>
<br>
参考链接:

[https://www.jianshu.com/p/e23078c11518](https://www.jianshu.com/p/e23078c11518)
