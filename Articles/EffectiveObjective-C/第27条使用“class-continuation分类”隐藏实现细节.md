# 第27条：使用“class-continuation分类”隐藏实现细节

**要点：**

* 通过“class-continuation分类”向类中新增实例变量。
* 如果某属性在主接口中声明为“只读”，而类的内部又要用设置方法修改此属性，那么就在“class-continuation分类”中将其扩展为“可读写”。
* 把私有方法的原型声明在“class-continuation分类”里面。
* 若想使类所遵循的协议不为人所知，则可于“class-continuation分类”中声明。

类中经常会包含一些无须对外公布的方法及实例变量。其实这些内容也可以对外公布，并且写明其为私有，开发者不应依赖它们。Objective-C 动态消息系统（参见第11条）的工作方式决定了其不可能实现真正的私有方法或私有实例变量。然而，我们最好还是只把确实需要对外公布的那部分内容公开。那么，这种不需对外公布但却应该具有的方法及实例变量应该怎么写呢？此时，这个特殊的“class-continuation分类”就派上用场了。

“class-continuation分类”和普通的分类不同，它必须定义在其所接续的那个类的实现文件里。其重要之处在于，这是唯一能声明实例变量的分类，而且此分类没有特定的实现文件，其中的方法都应该定义在类的主实现文件里。与其他分类不同，“class-continuation分类”没有名字。比如，有个类叫做 EOCPerson，其“class-continuation分类”写法如下：

```
@interface EOCPerson ()
// Methods here
@end
```

为什么需要有这种分类呢？**因为其中可以定义方法和实例变量**。为什么能在其中定义方法和实例变量呢？只因有“稳固的 ABI”这一机制（第6条详解了此机制），使得我们无需知道对象大小即可使用它。由于类的使用者不一定需要知道实例变量的内存布局，所以，它们也就未必非得定义在公共接口中了。基于上述原因，我们可以像在类的实现文件里那样，于“class-continuation分类”中给类新增实例变量。只需在适当位置上多写几个括号，然后把实例变量放进去。如下所示：

```
@interface EOCPerson () {
    NSString *_anInstanceVariable;
}
// Method declarations here
@end

@implementation EOCPerson {
    int _anotherInstanceVariable;
}
// Method implementations here
@end
```

这样做有什么好处呢？公共接口里本来就能定义实例变量。不过，把它们定义在“class-continuation分类”或“实现块”中可以将其隐藏起来，只供本类使用。即便在公共接口里将其标注为 private，也还是会泄漏实现细节。比方说，你有个绝密的类，不想给其他人知道。假设你所写的某个类拥有那个绝密类的实例，而这个实例变量又声明在公共接口里面：

```
#import <Foundation/Foundation.h>

@class EOCSuperSecretClass;

@interface EOCClass : NSObject {
@private
    EOCSuperSecretClass *_secretInstance;
}
@end
```

那么，信息就泄漏了，别人就会知道有个名叫 EOCSuperSecretClass 的类。为解决此问题，可以不把实例变量声明为强类型，而是将其类型由 EOCSuperSecretClass* 改为 id。然而这么做不够好，因为在类的内部使用此实例时，无法得到编译器的帮助。没必要只因为想对外界隐藏某个内容就放弃编译器的辅助检查功能吧？这个问题可以由“class-continuation分类”来解决。那个代表绝密类的实例可以声明成这样：

```
// EOCClass.h
#import <Foundation/Foundation.h>

@interface EOCClass : NSObject
@end

// EOCClass.m
#import "EOCClass.h"
#import "EOCSuperSecretClass.h"

@interface EOCClass () {
    EOCSuperSecretClass *_secretInstance;
}
@end

@implementation EOCClass
// Methods here
@end
```

实例变量也可以定义在实现块里，从语法上说，这与直接添加到“class-continuation分类”等效，只是看个人喜好罢了。笔者喜欢将其添加在“class-continuation分类”中，以便将全部数据定义都放在一处。由于“class-continuation分类”还能定义一些属性，所以在这里额外声明一些实例变量也很合适。这些实例变量并非真的私有，因为在运行期总可以调用某些方法绕过此限制。不过，从一般意义上来说，它们还是私有的。此外，由于没有声明在公共头文件里，所以将代码作为程序库的一部分发行时，其隐藏程度更好。

编写 Objective-C++代码时，“class-continuation分类”也有为有用。Objective-C++ 是 Objective-C 与 C++ 的混合体，其代码可以用这两种语言来编写。由于兼容性原因，游戏后端一般用 C++ 来写。另外，有时候要使用的第三方库可能只有 C++ 绑定，此时也必须使用 C++ 来编写。在这些情况下，使用“class-continuation分类”会很方便。假设某个类打算这样写：

```
#import <Foundation/Foundation.h>
#include "SomeCppClass.h"
@interface EOCClass : NSObject {
@private
    SomeCppClass _cppClass;
}
@end
```

该类的实现文件可能叫做 EOCClass.mm，其中.mm扩展名表示编译器应该将此文件按 Objective-C++ 来编译，否则，就无法正确引入 SomeCppClass.h 了。然而请注意，名为 SomeCppClass 的这个 C++ 类必须完全引入，因为编译器要完整地解析其定义方能得知 
_ cppClass 实例变量的大小。于是，只要是包含 EOCClass.h的类，都必须编译为 Objective-C++ 才行，因为它们都引入了 SomeCppClass 类的头文件。这很快就会失控，最终导致整个应用程序全部都要编译为 Objective-C++。这么做确实完全可行，不过笔者觉得相当别扭，尤其是将代码发布为程序库供其他应用程序使用时，更不应该如此。要求第三方开发者将其源文件扩展名均改为 .mm 不是很合适。

你可能认为解决此问题的办法是：不引入 C++ 类的头文件，只是向前声明该类，并且将实例变量做成指向此类的指针。

```
#import <Foundation/Foundation.h>
class SomeCppClass;

@interface EOCClass : NSObject {
@private
    SomeCppClass * _cppClass;
}
@end
```

现在实例变量必须是指针，若不是，则编译器无法得知其大小，从而会报错。但所有指针的大小确实都是固定的。于是编译器只需知道其所指的类型即可。不过，这么做还是会遇到刚才那个问题，因为引入 EOCClass 头文件的源码里都包含 class 关键字，而这是 C++ 的关键字，所以仍然需要按 Objective-C++来编译才行。这样做既别扭又无必要，因为该实例变量毕竟是 private 的，其他类为什么要知道它呢？这个问题还是得用“class-continuation分类”来解决。将刚才那个类改写之后，其代码如下：

```
// EOCClass.h
#import <Foundation/Foundation.h>

@interface EOCClass : NSObject
@end

// EOCClass.m
#import "EOCClass.h"
#include "SomeCppClass.h"

@interface EOCClass () {
    SomeCppClass _cppClass;
}
@end

@implementation EOCClass
@end
```

改写后的 EOCClass 类，其头文件里没有 C++代码了，使用头文件的人甚至意识不到其底层实现代码中混有 C++成分。某些系统库用到了这种模式，比如网页浏览器框架 WebKit，其大部分代码都以 C++ 编写，然而对外展示出来的却是一套整洁的 Objective-C 接口。CoreAnimation 里面也用到了此模式，它的许多后端代码都用 C++ 写成，但对外公布的却是一套纯 Objective-C 接口。













