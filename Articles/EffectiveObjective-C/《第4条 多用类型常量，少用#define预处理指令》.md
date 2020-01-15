## 第4条 多用类型常量，少用#define 预处理命令

**要点**

* 不要使用预处理指令定义常量。这样定义出来的常量不含类型信息，编译器只是会在编译前据此执行查找与替换操作。即使有人重新定义了常量值，编译器也不会产生警告信息，这将导致应用程序中的常量值不一致。
* 在实现文件中使用 static const 来定义“只在编译单元内可见的常量”。由于此类常量不在全局符号表中，所以无须为其名称加前缀。
* 在头文件中使用 extern 来声明全局常量，并在相关实现文件中定义其值。这种常量要出现在全局符号表中，所以其名称应加以区隔，通常用与之相关的类名做前缀。

编写代码时经常要定义常量。例如，要写一个 UI 视图类，此视图显示出来之后就播放动画，然后消失。你可能想把动画的时间提取为常量。如下所示：

```
#define ANIMATION_DURATION 0.3
```

上述预处理指令会把源代码中的 ANIMATION_DURATION 字符串替换为 0.3。不过，这样定义出来的常量没有类型信息。预处理过程会把碰到的所有 ANIMATION_DURATION 一律替换成 0.3，这样的话，假设此指令声明在某个头文件中，那么所有引入这个头文件的代码，其 ANIMATION_DURATION 都会被替换。

要想解决此问题，应该设法利用编译器的某些特性才对。有个办法比用预处理指令来定义常量更好。比如，下面这行代码就定义了一个类型为 NSTimeInterval 的常量：

```
static const NSTimeInterval kAnimationDuration = 0.3;
```

用此方式定义的常量包含类型信息，其好处是清楚地描述了常量的含义。由此可知该常量类型为 NSTimeInterval，这有助于为其编写开发文档。如果要定义许多常量，那么这种方式能令稍后阅读代码的开发者更易理解其意图。

还要注意常量名称。**常用的命名法是：若常量局限于某“编译单元”(translation unit)，也就是“实现文件”(implementation)之内，则在前面加字幕 k；若常量在类之外可见，则通常以类名为前缀**。第19条详解了命名习惯（naming convention）。

定义常量的位置很重要。我们总喜欢在头文件里声明预处理指令，这样做真的很糟糕，当常量名称有可能互相冲突时更是如此。例如，ANIMATION_DURATION 这个常量名就不该用在头文件中，因为所有引入了这份头文件的其他文件中都会出现这个名字。其实就连用 static const 定义的那个常量也不应该出现在头文件里。因为 Objective-C 没有“名称空间”(namespace)这一概念，所以那样做等于声明了一个名叫 kAnimationDuration 的全局变量。此名称应该加上前缀，以表明其所属的类，例如可改为 EOCViewClassAnimationDuration。本书第19条中深入讲解了一套清晰的命名方案。

**若不打算公开某个常量，则应将其定义在使用该常量的实现文件里**。比方说，要开发一个使用 UIKit 框架的 iOS 应用程序，其 UIView 子类中含有表示动画播放时间的常量，那么可以这样写：

```
// EOCAnimatedView.h
#import <UIKit/UIKit.h>
@interface EOCAnimatedView : UIView
- (void)animate;
@end

// EOCAnimatedView.m
#import "EOCAnimatedView.h"
static const NSTimeInterval kAnimationDuration = 0.3;

@implementation EOCAnimatedView
- (void)animate {
  [UIViewAnimateWithDuration:kAnimationDuration animations:^(){
      // Perform animations
  }];
}
@end
```

**变量一定要同时用 static与const来声明。如果试图修改由 const 修饰符所声明的变量，那么编译器就会报错。而 static 修饰符则意味着该变量仅在定义此变量的编译单元中可见**。编译器每收到一个编译单元，就会输出一份“目标文件”(object file)。在 Objective-C 的语境下，“编译单元”一词通常指每个类的实现文件（以.m为后缀名）。因此，在上述范例代码中声明的 kAnimationDuration 变量，其作用域仅限于由 EOCAnimatedView.m 所生成的目标文件中。假如声明此变量时不加 static，则编译器会为它创建一个“外部符号”(external symbol)。此时若是另一个编译单元中也声明了同名变量，那么编译器就抛出一条错误消息：

```
duplicate symbol _kAnimationDuration in:
  EOCAnimatedView.o
  EOCOtherView.o
```

实际上，如果一个变量既声明为 static，又声明为 const，那么编译器根本不会创建符号，而是会像 #define 预处理指令一样，把所有遇到的变量都替换为常值。不过还是要记住：用这种方式定义的常量**带有类型信息**。

为避免名称冲突，最好是用与之相关的类名做前缀。系统框架中一般都这样做。例如 UIKit就按照这种方式来声明用作通知名称的全局常量。其中有类似 UIApplicationDidEnterBackgroundNotification 与 UIApplicationWillEnterForegroundNotification这样的常量名。

其他类型的常量也是如此。假如要把前例中 EOCAnimatedView 类里的动画播放时长对外公布，那么可以这样声明：

```
// EOCAnimatedView.h
extern const NSTimeInterval EOCAnimatedViewAnimationDuration;

// EOCAnimatedView.m
const NSTimeInterval EOCAnimatedViewAnimationDuration = 0.3;
```

**这样定义常量要优于使用 #define 预处理指令，因为编译器会确保常量值不变。一旦在 EOCAnimatedView.m中定义好，即可随处使用。而采用预处理指令所定义的常量可能会无意中遭人修改，从而导致应用程序各个部分所使用的值互不相同**。

总之，勿使用预处理指令定义常量，而应该借助编译器来确保常量正确，比如可以在实现文件中使用 static const 来声明常量，也可以声明一些全局变量。





















