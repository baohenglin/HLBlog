# 第 17 条：实现 description 方法

调试程序时，经常需要打印并查看对象信息。一种办法是编写代码把对象的全部属性都输出到日志中。不过最常用的做法还是像下面这样：

```
NSLog(@"object = %@", object);
```

在构建需要打印到日志的字符串时，object 对象会收到 description 消息，该方法所返回的描述信息将取代“格式字符串”（format string）里的“%@”。比方说，object 是个数组，若用下列代码打印其信息：

```
NSArray *object = @[@"A string", @(123)];
NSLog(@"object = %@", object);
```

则会输出：

```
object = (
  "A string",
  123
)
```

然而，如果在**自定义的类**上这么做，那么输出的信息却是下面这样：

```
object = <EOCPerson: 0x7fd9a1600600>
```

与 object 为数组时所输出的信息相比，上面这种内容不太有用。**除非在自定义的类里覆写 description 方法**，否则打印信息时就会调用 NSObject 类所实现的默认方法。此方法定义在 **NSObject 协议**里，不过 **NSObject 类也实现了它**。因为 NSObject 并不是唯一的“根类”，所以许多方法都要定义在 NSObject 协议里。比方说，NSProxy 也是一个遵守了 NSObject 协议的“根类”。由于 description 等方法定义在 NSObject 协议里，因此像 NSProxy 这种“根类”及其子类也必须实现它们。如前所见，这些实现好的方法并没有打印出较为有用的内容，只不过是输出了**类名和对象的内存地址**。只有在你想判断两指针是否真的指向同一对象时，这种信息才有用处。除此之外，再也看不出其他有用的内容了。我们想打印出来的对象信息应该比这更多才对。

要想输出更为有用的信息也很简单，**只需覆写 description 方法并将描述此对象的字符串返回即可**。例如，有下面这个代表个人信息的类：

```
#import <Foundation/Foundation.h>

@interface EOCPerson : NSObject

@property (nonatomic, copy, readonly) NSString *firstName;
@property (nonatomic, copy, readonly) NSString *lastName;

- (id)initWithFirstName:(NSString*)firstName 
              lastName:(NSString*)lastName;
@end

@implementation EOCPerson

- (id)initWithFirstName:(NSString*)firstName
              lastName:(NSString*)lastName
{
  if (self = [super init]) {
    _firstName = [firstName copy];
    _lastName = [lastName copy];
  }
  return self;
}
@end
```

**该类的 description 方法通常可以这样实现**：

```
- (NSString *)description {
  return [NSString stringWithFormat:@"<%@: %p, \"%@ %@\">",[self class], self, _firstName, _lastName];
}
```

假如按上面的代码来写，那么 EOCPerson 对象就会输出如下格式的信息：

```
EOCPerson *person = [[EOCPerson alloc]initWithFirstName:@"Bob" lastName:@"Smith"];
NSLog(@"person = %@", person);
// Output:
// person = <EOCPerson: 0x7fb249c030f0, "Bob Smith"> 
```

这样就比覆写之前所输出的信息更加清楚，也更为有用了。笔者建议：在新实现的 description 方法中，也应该像默认的实现那样，打印出类的名字和指针地址。因为这些内容有时也许会用到。不过 NSArray 类的对象就没有打印这两项内容。显然在实现 description 方法时，没有固定规则可循，应根据当前对象来决定在 description 方法里打印何种信息。

有个简单的办法，可以在 description 中输出很多互不相同的信息，那就是**借助 NSDictionary 类的 description 方法**。此方法输出的信息的格式如下：

```
{
  key: value;
  foo: bar;
}
```

**在自定义的 description 方法中，把待打印的信息放到字典里面，然后将字典对象的 description 方法所输出的内容包含在字符串里并返回，这样就可以实现精简的信息输出方法了。










