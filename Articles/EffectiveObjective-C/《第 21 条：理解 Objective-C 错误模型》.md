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

错误发生的范围。也就是产生错误的根源。通常用一个特有的全局变量来定义。比方说，“处理URL的子系统”在从URL中解析或取得数据时如果出错了，那么就会使用NSURLErrorDomain来表示错误范围。

* Error code（错误码，其类型为整数）
 
 独有的错误代码，用以指明在某个范围内具体发生了何种错误。某个特定范围内可能会发生一系列相关错误，这些错误情况通常采用 enum 来定义。例如，当 HTTP 请求出错时，可能会把 HTTP 状态码设为错误码。

* User info（用户信息，其类型为字典）

有关错误的额外信息，其中或许包含一段“本地化的描述”（localized description），或许还含有导致该错误发生的另外一个错误，经由此种信息，可将相关错误串成一条“错误链”（chain of errors）

在设计 API 时，**NSError 的第一种常见用法是通过委托协议来传递此错误**。有错误发生时，当前对象会把错误信息经由协议中的某个方法传给其委托对象（delegate）。例如，NSURLConnection 在其委托协议 NSURLConnectionDelegate 之中就定义了如下方法：

```
- (void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error
```

当 NSURLConnection 出错之后（比如与远程服务器的连接操作超时了），就会调用此方法以处理相关错误。这个委托方法未必非得实现不可：是不是必须处理此错误，可交由 NSURLConnection 类的用户来判断。这比抛出异常要好，因为调用者至少可以自己决定 NSURLConnection 是否回报此错误。

**NSError 的另外一种常见用法是：经由方法的“输出参数”返回给调用者**。比如像这样：

```
- (BOOL)doSomething:(NSError**)error
```

传递给方法的参数是个指针，而该指针本身又指向另外一个指针，那个指针指向 NSError 对象。或者也可以把它当成一个直接指向 NSError 对象的指针。这样一来，此方法不仅能有普通的返回值，而且还能经由“输出参数”把 NSError 对象回传给调用者。其用法如下：

```
NSError *error = nil;
BOOL ret = [object doSomething:&error];
if (error) {
  // There was an error
}
```

像这样的方法一般都会返回 Boolean 值，用以表示该操作是成功了还是失败了。如果调用者不关注具体的错误信息，那么直接判断这个 Boolean 值就好；若是关注具体错误，那就检查经由“输出参数”所返回的那个错误对象。在不想知道具体错误的时候，可以给 error参数传入 nil。比方说，可以如下使用此方法：

```
BOOL ret = [object doSomething:nil];
if (ret) {
  // There was an error
}
```

实际上，在使用 ARC 时，编译器会把方法签名中的 NSError** 转换成 NSError*_ _ autoreleasing* ，也就是说，指针所指的对象会在方法执行完毕后自动释放。这个对象必须自动释放，因为“doSomething:”方法不能保证其调用者可以把此方法中创建的 NSError 释放掉，所以必须加入 autorelease。这就与大部分方法（以 new、alloc、copy、mutableCopy 开头的方法当然不在此列）的返回值所具备的语义相同了。

该方法通过下列代码把 NSError 对象传递到“输出参数”中：


