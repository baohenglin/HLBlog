Objective-C与C++、Java等面向对象语言类似，不过很多方面有所差别。Objective-C语言使用“消息结构”(messaging structure)而非“函数调用”(function calling)。消息与函数调用之间的区别看上去就像这样：

```
//Messaging (Objective-C)
Object *obj = [Object new];
[obj performWith:parameter1 and:parameter2];

//Function calling (C++)
Object *obj = new Object;
obj -> perform(parameter1, parameter2);
```

关键区别在于：使用消息结构的语言，其运行时所应执行的代码由运行环境决定；而使用函数调用的语言，则由编译器决定。如果范例代码中调用的函数是多态的，那么在运行时就要按照“虚方法表”(virtual table)来查出到底应该执行哪个函数实现。
