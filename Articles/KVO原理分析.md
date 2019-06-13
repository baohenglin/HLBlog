# KVO原理分析

## 1. KVO基础知识

&emsp;&emsp;KVO全称是Key-Value Observing,也称为“键值监听”，主要用于监听某个对象属性值的改变。

### 1.1 KVO的基本使用

```
#import "ViewController.h"
#import "HLPerson.h"
@interface ViewController ()
@property (nonatomic, strong) HLPerson *person;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    _person = [[HLPerson alloc]init];
    _person.age = 10;
    //给_person对象添加KVO监听
    NSKeyValueObservingOptions options = NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld;
    [self.person addObserver:self forKeyPath:@"age" options:options context:nil];
}

- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    //改变age值
    _person.age = 20;
}
//监听方法
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context
{
    NSLog(@"keyPath = %@\nobject = %@\nchange = %@",keyPath,object,change);
    /*打印结果:
     keyPath = age
     object = <HLPerson: 0x600002dc8a70>
     change = {
     kind = 1;
     new = 20;
     old = 10;
     }
     */
}
//移除监听
- (void)dealloc
{
    [self.person removeObserver:self forKeyPath:@"age"];
}
@end
```

## 2. KVO的本质探究

### 2.1 KVO的实现原理

&emsp;&emsp;我们分别打印对象_person1和_person2的isa指针。结果如下：

```
(lldb) po _person1->isa
NSKVONotifying_HLPerson
(lldb) po _person2.isa
HLPerson
```

由于实例对象的isa指针指向类对象，所以_person1的类对象是NSKVONotifying_HLPerson，_person2的类对象是HLPerson。由此可见，如果给某个实例对象添加了KVO监听，那么它的isa指针会指向一个全新的类对象（NSKVONotifying_HLPerson），而如果不添加KVO监听的话，isa指针还是指向原来的类对象。

（1）未使用KVO监听的对象的底层实现如下图所示：

![未使用KVO监听的对象](https://upload-images.jianshu.io/upload_images/4164292-20aacf99194a2133.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 '未使用KVO监听的对象')

（2）使用了KVO监听的对象的底层实现如下图所示：

![使用了KVO监听的对象](https://upload-images.jianshu.io/upload_images/4164292-44308711bc0fc44f.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 '使用了KVO监听的对象')

由上可知，KVO的实现原理是在运行时通过isa-swizzling技术动态地创建一个继承自当前类的派生类（NSKVONotifying_XXX），并且动态地修改当前实例对象的isa指针，使其指向派生类（NSKVONotifying_XXX），并重写了class方法，使其返回当前类的Class。派生类NSKVONotifying_HLPerson还重写了父类当中的setAge:方法,调用了Foundation中的_NSSetIntValueAndNotify(_NSSet*ValueAndNotify,可能是Int也可能是double),那么_NSSetIntValueAndNotify函数内部是如何实现的呢? 先调用willChangeValueForKey:方法，再调用父类当中的setAge:方法修改属性age的值，最后调用didChangeValueForKey:方法，didChangeValueForKey内部会触发监听器的监听回调函数（observeValueForKeyPath:key ofObjet:self change:nil context:nil）。

NSKVONotifying_HLPerson中的伪代码如下:

```
//重写了class方法、dealloc方法、_isKVOA方法、setAge方法
- (Class)class
{
    return [HLPerson class];
}
- (void)dealloc
{
    
}
- (BOOL)_isKVOA
{
    
}
- (void)setAge:(int)age
{
    _NSSetIntValueAndNotify();
}
//_NSSetIntValueAndNotify()函数的伪代码
void _NSSetIntValueAndNotify()
{
    [self willChangeValueForKey:@"age"];
    //调用父类的setAge:方法
    [super setAge:age];
    [self didChangeValueForKey:@"age"];
}
- (void)didChangeValueForKey:(NSString *)key
{
    //通知监听器,被监听对象的属性值发生了改变
    [oberser observeValueForKeyPath:key ofObjet:self change:nil context:nil];
}
```

### 2.2 派生类中重写了哪些方法？

派生类(NSKVONotifying_HLPerson)中重写了setAge方法、class方法、dealloc方法、_isKVOA方法。那么为什么苹果要重写class方法呢？因为苹果可能不想将生成的派生类(NSKVONotifying_HLPerson)暴露给开发者，所以采用重写-(Class)class方法的方式，屏蔽了内部实现，隐藏了动态生成的派生类(NSKVONotifying_HLPerson)。

&emsp;&emsp;如何验证派生类中重写了这些方法呢？可以通过以下代码来验证：

```
//cls参数表示类名
-(void)printMethodNamesOfClass:(Class)cls
{
    unsigned int count;
    
    Method *methodList = class_copyMethodList(cls, &count);
    NSMutableString *methodNames = [NSMutableString string];
    //遍历所有方法
    for (int i = 0; i < count; i++) {
        //获取方法
        Method method = methodList[i];
        //获得方法名
        NSString *methodName = NSStringFromSelector(method_getName(method));
        [methodNames appendString:methodName];
        [methodNames appendString:@","];
    }
    //class_copyMethodList或class_createMethodList出来的对象一般都需要释放。
    free(methodList);
    NSLog(@"cls = %@,methodNames = %@",cls,methodNames);
}
```
调用printMethodNamesOfClass:方法

```
[self printMethodNamesOfClass:object_getClass(_person1)];
[self printMethodNamesOfClass:object_getClass(_person2)];

```
打印结果如下：

```
cls = NSKVONotifying_HLPerson,methodNames = setAge:,class,dealloc,_isKVOA,
cls = HLPerson,methodNames = setAge:,age,
```
【总结】由打印结果可知，未添加KVO监听的实例对象的类对象中实现了属性的setter和getter方法，而添加了KVO监听所生成的派生类NSKVONotifying_HLPerson中实现了setAge:方法、class方法、dealloc方法、_isKVOA方法。


## 3. KVO总结

题目1：iOS用什么方式实现对一个对象的KVO？（KVO的本质是什么？）

&emsp;&emsp;利用Runtime动态生成一个子类，并且让instance对象的isa指向这个全新的子类，并且这个全新的子类重写了setter方法、class方法、dealloc方法、_isKVOA方法。当修改instance对象的属性时，重写的setter方法会调用Foundation的_NSSetXXXValueAndNotify函数，在_NSSetXXXValueAndNotify函数内部会先调用willChangeValueForKey:方法，然后调用父类原来的setter方法，最后调用didChangeValueForKey:方法，并在didChangeValueForKey方法内部触发监听器（Oberser）的监听方法（observeValueForKeyPath:ofObject:change:context:）。

题目2：如何手动触发KVO？

&emsp;&emsp;手动调用willChangeValueForKey:和didChangeValueForKey:这两个方法即可。

题目3：直接修改成员变量会触发KVO吗？

&emsp;&emsp;不会触发KVO(直接修改成员变量不会调用该属性的setter方法)。KVO的实现原理是利用Runtime动态生成一个子类，并且让instance对象的isa指向这个全新的子类，并且这个全新的子类重写了setter方法、class方法、dealloc方法、_isKVOA方法。当修改instance对象的属性时，重写的setter方法会调用Foundation的_NSSetXXXValueAndNotify函数，在_NSSetXXXValueAndNotify函数内部会先调用willChangeValueForKey:方法，然后调用父类原来的setter方法，最后调用didChangeValueForKey:方法，并在didChangeValueForKey方法内部触发监听器（Oberser）的监听方法（observeValueForKeyPath:ofObject:change:context:）。由于直接修改成员变量不会调用该属性的setter方法，所以不会触发KVO。

&emsp;&emsp;此时我们可以手动触发KVO。也就是手动调用willChangeValueForKey:和didChangeValueForKey:这两个方法。