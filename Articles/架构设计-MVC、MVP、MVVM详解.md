
## 架构

架构(Architecture)，架构可大可小，大到客户端与服务器端的关系，小到模块与模块之间的关系，类与类之间的关系。在iOS开发中，涉及到的架构模式主要有MVC、MVVM、MVP、VIPER等。MVC、MVP、MVVM等架构模式都是为了解决界面呈现和逻辑代码分离而出现的模式。其中，MVP和MVVM都是在MVC的基础上演化而来。这些架构的目的都是为了提高代码的复用率、降低代码的耦合度、便于模块测试。

## MVC

### MVC-Apple版

![Apple版MVC示意图.png](https://upload-images.jianshu.io/upload_images/4164292-5ff93fa8ef79656d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

M（Model）：模型，模型对象封装了应用程序的数据，并对该数据进行相应的逻辑处理。

V（View）：视图，负责显示应用程序的数据，也就是展示层（GUI）

C（Controller）：控制器，负责协调Model和View。通常根据用户在View层上的操作使Model层做出相应的更改，同时将更改的信息更新到View上。

Controller创建并持有View，View(View内部的点击事件、滚动事件等)通过action向Controller报告事件的发生；Controller可以控制(加载数据、更新数据)Model层的数据，同时Model层数据回调给Controller，并将最新的数据更新到View上。也就是说，Model和View之间没有任何关联，Model与View永远无法直接相互通信，它们需要通过Controller来协调，Controller相当于Model和View之间的桥梁。

MVC-Apple版的优点：View和Model之间耦合度低，View和Model可以复用，也可以独立使用

MVC-Apple版的缺点：Controller的代码过于臃肿


### MVC变种

![变种版MVC示意图.png](https://upload-images.jianshu.io/upload_images/4164292-23569eb85f1ddab8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

MVC变种的优点：Controller瘦身，Controller的代码不会过于臃肿

MVC变种的缺点：View和Model之间耦合度变高


## MVP（Model-View-Presenter）

![MVP示意图.png](https://upload-images.jianshu.io/upload_images/4164292-ac79f48aace5dafa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

MVP由3部分组成：

Model：负责处理、提供数据

View：负责绘制显示UI、与用户进行交互

Presenter：负责业务逻辑，Presenter是View和Model的之间通信的中间纽带。

MVP的本质上是MVC架构中Controller层的逻辑处理代码移到了Presenter中，这样就解决了传统MVC架构中Controller代码过于臃肿的问题。MVP适用于中小型项目，大型项目慎用。

MVP的优点：代码逻辑清晰，便于单元测试

MVP的缺点：代码量大


## MVVM

MVVM是Model-View-ViewModel的简写。MVVM在使用当中，通常还会利用双向绑定技术，使得Model变化时，ViewModel会自动更新，而ViewModel变化时，View也会自动变化，所以MVVM模式有时候又被称作model-view-binder模式。具体在iOS中，可以使用KVO或Notification技术达到这种效果。

MVVM确实能够使得Model层和View层解耦，但是如果你需要实现MVVM中的双向绑定的话，那么就需要引入ReactiveCocoa这种响应式框架。

这种引入ReactiveCocoa响应式框架的MVVM缺点主要有以下两点：

* (1)数据双向绑定使得Bug很难被定位。你看到界面异常了，有可能是你 View 的代码有 Bug，也可能是 Model 的代码有问题。数据绑定使得一个位置的 Bug 被快速传递到别的位置，要定位原始出问题的地方就变得不那么容易了。

* (2)对于过大的项目，数据绑定需要花费更多的内存。

其实实际项目中，我们仅仅需要一个ViewModel而已，我们完全可以简单地创建一个ViewModel的工厂类就可以了，没有必要引入那么复杂的响应式框架。


## 如何对ViewController瘦身？

虽然MVC只有三层，但是它并没有限制你只能有三层。我们可以将ViewController中过于臃肿的代码逻辑抽取出来，形成新的可复用模块或架构层次。比如将UITableView的Data Source分离到另外一个类中；将数据获取和数据转换的逻辑分离到另外一个类中；将控件拼接的逻辑分离到另外一个类中等等。

具体可以从以下几方面来对ViewController进行瘦身：

(1)将网络请求封装成单独的类。这样做的好处是可以将网络请求与具体的第三方库依赖隔离，方便以后更换底层的网络库；还可以方便地在基类中处理公共逻辑(JSON合法性检查)；还可以方便地在基类中处理缓存逻辑。

(2)将界面的拼装抽象到专门的类中。也就是构造专门的UIView的子类，来负责这些控件的拼装。

(3)构造ViewModel。具体做法是将ViewController给View传递数据这个过程，抽象成构造ViewModel的过程。这样，View只接受ViewModel，而Controller只需要传递ViewModel这么一行代码。

(4)构造专门的数据存储类。


## 三层架构 VS 四层架构

三层架构是指将整个架构分为以下三层：

* (1)界面层：UI页面，之前涉及的MVC、MVP、MVVM等架构模式都属于界面层。

* (2)业务层：加载数据等逻辑处理

* (3)数据层：网络请求或本地数据库操作等

四层架构是指将整个App架构分为以下四层：

* (1)界面层

* (2)业务层

* (3)网络层

* (4)本地数据层


## 设计模式

设计模式（Design Pattern）是一套被反复使用、代码设计经验的总结。使用设计模式的好处是可重用代码、让代码更容易被他人理解、保证代码的可靠性。

设计模式可以分为三大类：

* 创建型模式：对象实例化的模式，用于解耦对象的实例化过程。比如**单例模式**、工厂方法模式等等

* 结构型模式：把类或对象结合在一起形成一个更大的结构。比如代理模式、适配器模式、组合模式、装饰模式等

* 行为型模式：类或对象之间如何交互以及划分责任和算法，比如**观察者模式**、命令模式、责任链模式等。




## 总结

1. 简述你对MVC、MVVM、MVP的理解以及你在项目里具体是怎么用的？

2. 你自己用过哪些设计模式？

3. 一般开始做一个项目，你的架构是如何思考的？
