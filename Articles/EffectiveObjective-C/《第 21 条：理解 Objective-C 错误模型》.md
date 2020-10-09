# 第 21 条：理解 Objective-C 错误模型

## 要点：

* 只有发生了可使整个应用程序崩溃的严重错误时，才应使用异常。
* 在错误不那么严重的情况下，可以指派“委托方法”（delegate method）未处理错误，也可以把错误信息放在 NSError 对象里，经由“输出参数”返回给调用者。

当前很多编程语言都有“异常”（exception）机制，Objective-C也不例外。写过 Java 代码的程序员应该很习惯于用异常来处理错误。如果你也是这么使用异常的，那现在就把它忘了吧，我们得从头学起。

首先**要注意的是，“自动引用计数”（Automatic Reference Counting，ARC，参见第 30 条）在默认情况下不是“异常安全的”（exception safe）。具体来说，这意味着：如果抛出异常，那么本应该在作用域末尾释放的对象现在却不会自动释放了。如果想生成“异常安全”的代码，可以通过设置编译器的标志来实现，不过这将引入一些额外代码，在不抛出异常时，也照样要执行这部分代码。需要打开的编译器标志叫做“-fobjc-arc-exceptions”**。

即使不用 ARC，也很难写出抛出异常时不会导致内存泄漏的代码。比方说，假设有段代码先创建好了某个资源，使用完之后再将其释放。可是在释放资源之前如果抛出异常了，那么该资源就不会被释放了：

```
id someResource = /* ... */;
if (/* check for error */) {
  @throw [NSException exceptionWithName:@"ExceptionName"
                                    reson:@"There was an error"
                                    userInfo:nil];
}
[someResource doSomething];
[someResource release];
```

在抛出异常之前先释放 someResource，这样做当然能解决此问题，不过要是待释放的资源有很多，而且代码的执行路径更为复杂的话，那么释放资源的代码就容易写得很乱。此外，代码中加入了新的资源之后，开发者经常会忘记在抛出异常前先把它释放掉。

### 致命错误（fatal error）

Objective-C语言现在所采用的办法是：只在极其罕见的情况下抛出异常，异常抛出之后，无须考虑恢复问题，而且应用程序此时也应该退出。这就是说，不用再编写复杂的“异常安全”代码了。

**异常只应该用于极其严重的错误，比如说，你编写了某个抽象基类，它的正确用法是先从中继承一个子类，然后使用这个子类。在这种情况下，如果有人直接使用了这个抽象基类，那么可以考虑抛出异常**。与其他语言不同，Objective-C中没有办法将某个类标识为“抽象类”。要想达成类似效果，最好的办法是在那些子类必须覆写的超类方法里抛出异常。这样的话，只要有人直接创建抽象基类的实例并使用它，即会抛出异常：

```
- (void)mustOverrideMethod {
  NSString *reason = [NSString stringWithFormat:@"%@ must be overrideden",NSStringFromSelector(_cmd)];
  @throw [NSException exceptionWithName:NSInternalInconsistencyException reason:reason userInfo:nil];
}
```

### 非致命错误（nonfatal error）

既然异常只用于处理严重错误（fatal error，致命错误），那么对其他错误怎么办呢？在出现“不那么严重的错误”（nonfatal error）时，Objective-C 语言所用的编程范式为：**令方法返回 nil/0，或是使用 NSError，以表明其中有错误发生**。比方说，如果初始化方法无法根据传入的参数来初始化当前实例，那么就可以令其返回 nil/0:

```
- (id)initWithValue:(id)value {
  if (self = [super init]) {
    if (/* Value means instance can't be created */) {
      self = nil;
    } else {
      // Initialize instance
    }
  }
  return slef;
}
```

在这种情况下，如果 if 语句发现无法用传入的参数值来初始化当前实例（比如这个方法要求传入的value参数必须是 non-nil 的），那么就把 self 设置成 nil，这样的话，整个方法的返回值也就是 nil 了。调用者发现初始化方法并没有把实例创建好，于是便可确定其中发生了错误。

NSError 的用法更加灵活，因为经由此对象，我们可以把导致错误的原因回报给调用者。NSError 对象里封装了三条信息：

* Error domain（错误范围，其类型为字符串）
* Error code（错误码，其类型为整数）
* User info（用户信息，其类型为字典）

