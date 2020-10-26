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
   copy->_friends = [_friends mutableCopy];
   return copy;
}
@end
```

这次所实现的方法比原来多了一些代码，它把本对象的_friends 实例变量复制了一份，令 copy 对象的_friends 实例变量指向这个复制过的 set。注意，这里使用了 -> 语法，因为_friends 并非属性，只是个在内部使用的实例变量。其实也可以声明一个属性来表示它，不过**由于该变量不会在本类之外使用**，所以没必要那么做。

为什么要拷贝_friends 实例变量呢？不拷贝这个变量，直接令两个对象共享同一个可变的 set 是否更简单些呢？如果真的那样做了，那么在给原来的对象添加一个新朋友后，拷贝过的那个对象居然也“神奇地”（magically）与之为友了。在本例中，这显然不是我们想要的效果。然而，那个 set 若是不可变的，则无须复制，因为其中的内容毕竟不会改变，所以不用担心此类问题。如果复制了，那么内存中将会有两个一模一样的 set，反而造成浪费。








