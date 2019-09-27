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





