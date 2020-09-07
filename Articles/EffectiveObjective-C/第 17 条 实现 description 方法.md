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
