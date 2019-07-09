
## 架构

架构(Architecture)，架构可大可小，大到客户端与服务器端的关系，小到模块与模块之间的关系，类与类之间的关系。在iOS开发中，涉及到的架构模式主要有MVC、MVVM、MVP、VIPER、CDD等。

## MVC

### MVC-Apple版

![Apple版MVC示意图.png](https://upload-images.jianshu.io/upload_images/4164292-5ff93fa8ef79656d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

M（Model）：模型，模型对象封装了应用程序的数据，并定义操控和处理该数据的逻辑和运算。

V（View）：视图，负责显示应用程序的数据，也就是展示层（GUI）

C（Controller）：控制器，负责协调Model和View。通常根据用户在View层上的操作使Model层做出相应的更改，同时将更改的信息更新到View上。

Controller创建并持有View，View(View内部的点击事件、滚动事件等)可以通知Controller来处理对应的操作逻辑；Controller可以控制(加载数据、更新数据)Model层的数据，同时Model层数据回调给Controller，并将最新的数据更新到View上。也就是说，Model和View之间没有任何关联，Model数据无法直接显示到View上，它们需要通过Controller来协调，Controller相当于Model和View之间的桥梁。

MVC-Apple版的优点：View和Model之间耦合度低，View和Model可以重复利用，也可以独立使用

MVC-Apple版的缺点：Controller的代码过于臃肿


### MVC变种

![变种版MVC示意图.png](https://upload-images.jianshu.io/upload_images/4164292-23569eb85f1ddab8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

MVC变种的优点：Controller瘦身，Controller的代码不会过于臃肿

MVC变种的缺点：View和Model之间耦合度变高


## MVP

![MVP示意图.png](https://upload-images.jianshu.io/upload_images/4164292-ac79f48aace5dafa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

MVP的本质上是MVC架构中Controller层的逻辑处理代码移到了Presenter中，这样就解决了传统MVC架构中Controller代码过于臃肿的问题。


## 总结

1. 简述你对MVC、MVVM、MVP的理解以及你在项目里具体是怎么用的？

2. 你自己用过哪些设计模式？

3. 一般开始做一个项目，你的架构是如何思考的？
