# 第 22 条 理解 NSCopying 协议

## 要点：

* 若想令自己所写的对象具有拷贝功能，则需实现 NSCopying 协议。
* 如果自定义的对象分为可变版本和不可变版本，那么就要同时实现 NSCopying 与 NSMutableCopying 协议。
* 复制对象时需决定采用浅拷贝还是深拷贝，一般情况下应该尽量执行浅拷贝。
* 如果你所写的对象需要深拷贝，那么可考虑新增一个专门执行深拷贝的方法。

使用对象时经常需要拷贝它。在 Objective-C 中，此操作通过 copy 方法完成。如果想令自己的类支持拷贝操作，那就要实现 NSCopying 协议，该协议只有一个方法：

```
- (id)copyWithZone:(NSZone*)zone
```

为何会出现 NSZone 呢？因为以前开发程序时，会据此把内存分成不同的“区”（zone），而对象会创建在某个区里面。现在不用了，每个程序只有一个区：“默认区”（default zone）。所以说，尽管必须实现这个方法，但是你不必担心其中的 zone 参数。

copy 方法由 NSObject 实现，该方法只是以“默认区”为参数来调用“copyWithZone:”。我们总是想覆写 copy 方法，其实**真正需要实现的却是“copyWithZone:”方法**，这个问题一定要注意。

若想使某个类支持拷贝功能，只需声明该类遵从 NSCopying 协议，并实现其中的那个方法即可。比方说，有个表示个人信息的类，可以在其接口定义中声明此类遵从 NSCopying 协议：

```
#import <Foundation/Foundation.h>
@interface EOCPerson : NSObject <NSCopying>

@property (nonatomic, copy, readonly) NSString *firstName;
@property (nonatomic, copy, readonly) NSString *lastName;

- (id)initWithFirstName:(NSString*)firstName
            andLastName:(NSString*)lastName;
@end
```

然后，实现协议中规定的方法：

```
- (id)copyWithZone:(NSZone*)zone {
  EOCPerson *copy = [[[self class] allocWithZone:zone]
                      initWithFirstName:_firstName
                            andLastName:_lastName];
  return copy;
}
```

在本例所实现的“copyWithZone:”中，我们直接把待拷贝的对象交给“全能初始化方法”（designated initializer），令其执行所有初始化工作。然而有的时候，除了要拷贝对象，还要完成其他一些操作，比如类对象中的数据结构可能并未在初始化方法中设置好，需要另行设置。

举个例子，假如 EOCPerson 中含有一个数组，与其他 EOCPerson 对象建立或解除朋友关系的那些方法都需要操作这个数组。那么在这种情况下，你得把这个包含朋友对象的数组也一并拷贝过来。下面列出了实现此功能所需的全部代码：

```
#import <Foundation/Foundation.h>

@interface EOCPerson : NSObject<NSCopying>

@property (nonatomic, copy, readonly) NSString *firstName;
@property (nonatomic, copy, readonly) NSStriing *lastName;

- (id)initWithFirstName:(NSString*)firstName
              andLastName:(NSString*)lastName;
- (void)addFriend:(EOCPerson*)person;
- (void)removeFriend:(EOCPerson *)person;

@end

@implementation EOCPerson {
   NSMutableSet *_friends;
}

- (id)initWithFirstName:(NSString*)firstName
            andLastName:(NSString*)lastName {
   if ((self = [super init])) {
     _firstName = [firstName copy];
     _lastName = [lastName copy];
     _friends = [NSMutableSet new];
   }
   return self;
}
- (void)addFriend:(EOCPerson*)person {
  [_friends addObject:person];
}
- (void)removeFriend:(EOCPerson*)person {
  [_friends removeObject:person];
}
- (id)copyWithZone:(NSZone*)zone {
  EOCPerson *copy = [[[self class] allocWithZone:zone]
                     initWithFirstName:_firstName
                           andLastName:_lastName];
   copy->_friends = [_friends mutableCopy]; //浅拷贝
   return copy;
}
@end
```

这次所实现的方法比原来多了一些代码，它把本对象的_friends 实例变量复制了一份，令 copy 对象的_friends 实例变量指向这个复制过的 set。注意，这里使用了 -> 语法，因为_friends 并非属性，只是个在内部使用的实例变量。其实也可以声明一个属性来表示它，不过**由于该变量不会在本类之外使用**，所以没必要那么做。

为什么要拷贝_friends 实例变量呢？不拷贝这个变量，直接令两个对象共享同一个可变的 set 是否更简单些呢？如果真的那样做了，那么在给原来的对象添加一个新朋友后，拷贝过的那个对象居然也“神奇地”（magically）与之为友了。在本例中，这显然不是我们想要的效果。然而，那个 set 若是不可变的，则无须复制，因为其中的内容毕竟不会改变，所以不用担心此类问题。如果复制了，那么内存中将会有两个一模一样的 set，反而造成浪费。

通常情况下，应该像本例这样，采用全能初始化方法来初始化待拷贝的对象。不过有些时候不能这么做，因为全能初始化方法会产生一些“副作用”（side effect），这些附加操作对目前要拷贝的对象无益。比如，初始化方法可能要设置一个复杂的内部数据结构，可是在拷贝后的对象中，这个数据结构立刻就要用其他数据来覆写，所以没必要再设置一遍。

上面的“copyWithZone:”方法中存放朋友对象的那个 set 是通过 mutableCopy 方法来复制的。此方法来自另一个叫做 NSMutableCopying 的协议。该协议与 NSCopying 类似，也只定义了一个方法：

```
- (id)mutableCopyWithZone:(NSZone*)zone
```

**如果你的类分为可变版本（mutable variant）与不可变版本（immutable variant），那么就应该实现 NSMutableCopying。若采用此模式，则在可变类中覆写“copyWithZone:”方法时，不要返回可变的拷贝，而应返回一份不可变的版本。无论当前实例是否可变，若需获取其可变版本的拷贝，均应调用 mutableCopy 方法。同理，若需要不可变的拷贝，则总应通过 copy 方法来获取**。

在可变对象上调用 copy 方法会返回另外一个不可变类的实例。这样做是为了能在可变版本与不可变版本质检自有切换。

```
[NSMutableArray copy] <=> NSArray //完全等价
[NSArray mutableCopy] <=> NSMutableArray
```

需要注意的是，**在可变对象上调用 copy 方法会返回另外一个不可变的实例**。这样做是为了能在可变版本和不可变版本之间自由切换。要实现此目标，还有个办法，就是提供三个方法：copy、immutableCopy、mutableCopy，其中，copy 所返回的拷贝对象与当前对象的类型一致，而另外两个方法则分别返回不可变版本与可变版本的拷贝。但是如果调用者并不知道其所用的实例是否真的可变，那么这种做法就不太好了。某个方法可能会把 NSMutableArray 对象当作 NSArray 返回给你，而你在上面调用 copy 方法来复制它。此时你以为拷贝后的对象应该是不可变的数组，但实际上它却是可变的。

在编写拷贝方法时，还要决定一个问题，就是应该执行“**深拷贝**”（deep copy）还是“**浅拷贝**”（shallow copy）。**深拷贝是指在拷贝对象自身时，将其底层数据也一并复制过去**。Foundation 框架中的所有 collection 类在默认情况下都执行**浅拷贝，也就是说，只拷贝容器对象本身，而不复制其中数据**。这样做的主要原因在于，容器内的对象未必都能拷贝，而且调用者也未必想在拷贝容器时一并拷贝其中的每个对象。

**深拷贝和浅拷贝的区别：浅拷贝之后的内容与原始内容均指向相同对象，而深拷贝之后的内容所指向的对象是原始内容中相关对象的一份拷贝**。

一般情况下，我们会遵照系统框架所使用的那种模式，在自定义的类中以浅拷贝的方式实现“copyWithZone:”方法。但如果有必要的话，也可以增加一个执行深拷贝的方法。以 NSSet 为例，该类提供了下面这个初始化方法，用以执行深拷贝：

```
- (id)initWithSet:(NSArray*)array copyItems:(BOOL)copyItems
```

若 copyItem 参数设为 YES，则该方法会向数组中的每个元素发送 copy 消息，用拷贝好的元素创建新的 set，并将其返回给调用者。

在 EOCPerson 那个例子中，存放朋友对象的 set 是用“copyWithZone:”方法来拷贝的，根据刚才讲的内容可知，这种浅拷贝方式不会逐个复制 set 中的元素。若需要深拷贝的话，则可像下面这样，编写一个专供深拷贝使用的方法：

```
- (id)deepCopy {
  EOCPerson *copy = [[[self class] allocWithZone:zone]
                     initWithFirstName:_firstName
                           andLastName:_lastName];
   copy->_friends = [[NSMutableSet alloc] initWithSet:_friends
                                            copyItems:YES]; //深拷贝
   return copy;
}
```

因为没有专门定义深拷贝的协议，所以其具体执行方式由每个类来确定，你只需决定自己所写的类是否要提供深拷贝方法即可。另外，不要假定遵从了 NSCopying 协议的对象都会执行深拷贝。在绝大多数情况下，执行的都是浅拷贝。如果需要在某对象上执行深拷贝，那么除非该类的文档说它是用深拷贝来实现 NSCopying 协议的，否则，要么寻找能够执行深拷贝的相关方法，要么自己编写方法来做。




