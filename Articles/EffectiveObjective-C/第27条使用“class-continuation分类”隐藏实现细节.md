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

为什么需要有这种分类呢？**因为其中可以定义方法和实例变量**(应用场景1)。为什么能在其中定义方法和实例变量呢？只因有“稳固的 ABI”这一机制（第6条详解了此机制），使得我们无需知道对象大小即可使用它。由于类的使用者不一定需要知道实例变量的内存布局，所以，它们也就未必非得定义在公共接口中了。基于上述原因，我们可以像在类的实现文件里那样，于“class-continuation分类”中给类新增实例变量。只需在适当位置上多写几个括号，然后把实例变量放进去。如下所示：

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

“class-continuation分类”还有一种合理的用法，就是**将 public 接口中声明为“只读”的属性扩展为“可读写”**(应用场景2)，以便在类的内部设置其值。我们通常不直接访问实例变量，而是通过设置访问方法来做（参见第7条），因为这样能够触发“键值观察”（Key-Value Observing，KVO）通知，其他对象有可能监听此事件。出现在“class-continuation分类”或其他分类中的属性必须同类接口里的属性具备相同的特质（attribute），不过，其“只读”状态可以扩展为“可读写”。例如，有个描述个人信息的类，其公共接口如下：

```
#import <Foundation/Foundation.h>

@interface EOCPerson : NSObject
@property (nonatomic, copy, readonly) NSString *firstName;
@property (nonatomic, copy, readonly) NSString *lastName;
- (id)initWithFirstName:(NSString *)firstName lastName:(NSString *)lastName;
@end
```

我们一般会在“class-continuation分类”中把这两个属性扩展为“可读写”：

```
@interface EOCPerson ()
@property (nonatomic, copy, readonly, readwrite) NSString *firstName;
@property (nonatomic, copy, readonly, readwrite) NSString *lastName;
@end
```

现在 EOCPerson 的实现代码可以随意调用“setFirstName:”或“setLastName:”这两个设置方法，也可以用“点语法”来设置属性。这样做很有用，既能令外界无法修改对象，又能在其内部按照需要管理其数据。这样，封装在类中的数据就由实例本身来控制，而外部代码则无法修改其值。第18条曾详述这一话题。请注意，若观察者（observer）正读取属性值而内部代码又在写入该属性时，则有可能引发“竞争条件”（race condition）。合理使用同步机制（参见第41条）能缓解此问题。

**只会在类的实现代码中用到的私有方法也可以声明在“class-continuation分类”中**(应用场景3)。这么做比较合适，因为它描述了那些只在类实现代码中才会使用的方法。这些方法可以这样写：

```
@interface EOCPerson ()
- (void)p_privateMethod;
@end
```

此处根据第20条所述的建议为方法名加了前缀，以体现其私有方法。新版编译器不强制要求开发者在使用方法之前必须先声明。然而像上面这样在“class-continuation分类”中声明一下通常还是有好处的，因为这样做可以把类里所含的相关方法统一描述于此。笔者在编写类的实现代码之前，经常喜欢像这样先把方法原型写出来，然后再逐个实现。要想使类的代码更易读懂，可以试试这个好办法。

最后还要讲一种用法：**若对象所遵从的协议只应视为私有，则可在“class-continuation分类”中声明**(应用场景4)。有时由于对象所遵从的某个协议在私有 API 中，所以我们可能不太想在公共接口中泄漏这一信息。比如，EOCPerson遵从了名为EOCSecretDelegate的协议。如果声明在公共接口里，那么要像下面这样来写：

```
#import <Foundation/Foundation.h>
#import "EOCSecretDelegate.h"
@interface EOCPerson : NSObject<EOCSecretDelegate>
@property (nonatomic, copy, readonly) NSString *firstName;
@property (nonatomic, copy, readonly) NSString *lastName;
- (id)initWithFirstName:(NSString *)firstName lastName:(NSString *)lastName;
@end
```
你可能会说，只需要向前声明EOCSecretDelegate协议就可以不引入它了（或者说，不引入定义该协议的头文件了）。用下面这行向前声明语句来取代 #import 指令：

```
@protocol EOCSecretDelegate;
```

但是这样一来，只要引入 EOCPerson头文件的地方，编译器都会给出下列警告信息：

```
warning:cannot find protocol definition for 'EOCSecretDelegate'
```

由于编译器看不到协议的定义，所以无法得知其中所含的方法，于是就会像这样警告开发者。然而，这毕竟是个私有的内部协议，你甚至连名字都不想让别人知道。此时还得请“class-continuation分类”来帮忙。不要在公共接口中声明 EOCPerson类遵从了 EOCSecretDelegate协议，而是改到“class-continuation分类”里面声明：

```
#import "EOCPerson.h"
#import "EOCSecretDelegate.h"

@interface EOCPerson () <EOCSecretDelegate>
@end

@implementation EOCPerson
/* …… */
@end
```

公共接口内所有提到 EOCSecretDelegate 的地方都可删去。这个私有协议现在已经不为外界所知了。使用 EOCPerson 的人若不深入探索一番，则很难发现其身影。














