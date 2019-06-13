# KVC底层原理分析

KVC的全称是Key-Value Coding，俗称“键值编码”，可以通过一个key来访问某个属性。

KVC常用的API有：

* -(void)setValue:(id)value forKeyPath:(NSString *)keyPath;
* -(void)setValue:(id)value forKey:(NSString *)key;
* -(id)valueForKeyPath:(NSString *)keyPath;
* -(id)valueForKey:(NSString *)key;

前两个是用来设置属性值的，后两个是用来获取属性值的。

## 赋值方法setValue:forKey:的原理

setValue:forKey:的原理示意图，如下：

![setValueForKey原理示意图.png](https://upload-images.jianshu.io/upload_images/4164292-35e4ef0e09971774.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

setValue:forKey:底层实现的方法调用顺序如下：

（1）首先会按照顺序依次查找setKey:方法和_setKey:方法，只要找到这两个方法当中的任何一个就直接传递参数，调用方法；

（2）如果没有找到setKey:和_setKey:方法，那么这个时候会查看accessInstanceVariablesDirectly方法的返回值，如果返回的是NO（也就是不允许直接访问成员变量），那么会调用setValue:forUndefineKey:方法，并抛出异常“NSUnknownKeyException”；

（3）如果accessInstanceVariablesDirectly方法返回的是YES，也就是说可以访问其成员变量，那么就会按照顺序依次查找 _key、_isKey、key、isKey这四个成员变量，如果查找到了，就直接赋值；如果依然没有查到，那么会调用setValue:forUndefineKey:方法，并抛出异常“NSUnknownKeyException”。


## 取值方法valueForKey:的原理

![valueForKey取值的原理示意图.png](https://upload-images.jianshu.io/upload_images/4164292-787bb4363920340c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


valueForKey:方法底层实现原理如下：

（1）首先会按照顺序依次查找getKey:、key、isKey、_key:这四个方法，只要找到这四个方法当中的任何一个就直接调用该方法；

（2）如果没有找到，那么这个时候会查看accessInstanceVariablesDirectly方法的返回值，如果返回的是NO（也就是不允许直接访问成员变量），那么会调用valueforUndefineKey:方法，并抛出异常“NSUnknownKeyException”；

（3）如果accessInstanceVariablesDirectly方法返回的是YES，也就是说可以访问其成员变量，那么就会按照顺序依次查找 _key、_isKey、key、isKey这四个成员变量，如果找到了，就直接取值；如果依然没有找到成员变量，那么会调用valueforUndefineKey方法，并抛出异常“NSUnknownKeyException”。

## KVC总结


题目1:通过KVC修改属性会触发KVO吗?原理是什么？

答:会触发KVO。即使没有声明属性，只有成员变量，只要accessInstanceVariablesDirectly返回的是YES，允许访问其成员变量，那么不管有没有调用setter方法，通过KVC修改成员变量的值，都能触发KVO。这也说明通过KVC内部实现了willChangeValueForKey:方法和didChangeValueForKey:方法。

题目2：KVC的赋值和取值过程是怎样的？（原理是什么？）

参考上面的赋值和取值示意图。

