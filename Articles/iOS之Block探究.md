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


## block的变量捕获（capture）

为了保证block内部能够正常访问外部的变量，block有一个变量捕获机制。什么是block的变量捕获机制呢？block的变量捕获机制是指block底层的结构体内部会专门新增一个成员变量用于存储捕获到的外部值。block的变量捕获分为以下几种：

![block变量捕获的几种类型](https://upload-images.jianshu.io/upload_images/4164292-c4d296b626735bf4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'block变量捕获的几种类型')

* auto修饰符：auto修饰的变量称为自动变量，也就是说auto变量离开作用于就立即销毁。默认情况下，默认使用auto修饰。访问方式是“值传递”。
* static修饰符：使用static修饰的变量一直会存储在内存中，访问方式是“地址传递”。

只要是局部变量，不管是auto修饰的还是static修饰的局部变量，block都可以成功捕获。而全局变量不会被捕获到block内部(因为全局变量都可以访问，无须捕获到block内部)。

## block常见的面试题
(1)block的本质是什么?底层原理是怎样的?

block本质上是一个OC对象,因为它内部有一个isa指针。更确切地说，block是封装了函数调用以及函数调用环境的OC的对象。

(2)__block的作用是什么?使用时需要注意什么?

(3)block的属性修饰词为什么是copy？使用block有哪些注意事项？

(4)block在修改NSMutableArray时，需不需要加__block？

<br>
<br>
<br>
参考链接:

[https://www.jianshu.com/p/e23078c11518](https://www.jianshu.com/p/e23078c11518)