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

"&"(取址符)表示取址。“&”右面是函数调用，__main_block_impl_0(); __main_block_impl_0函数有三个参数__main_block_func_0、age。



## block常见的面试题
(1)block的本质是什么?底层原理是怎样的?

block本质上是一个OC对象,因为它内部有一个isa指针。更确切地说，block是封装了函数调用以及函数调用环境的OC的对象。

(2)__block的作用是什么?使用时需要注意什么?

(3)block的属性修饰词为什么是copy？使用block有哪些注意事项？

(4)block在修改NSMutableArray时，需不需要加__block？