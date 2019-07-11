## Protocols（协议）

A protocol defines a blueprint of methods, properties, and other requirements that suit a particular task or piece of functionality. The protocol can then be adopted by a class, structure, or enumeration to provide an actual implementation of those requirements. Any type that satisfies the requirements of a protocol is said to conform to that protocol.

协议 定义了一个蓝图，规定了用来实现某一特定任务或者功能的方法、属性，以及其他需要的东西。类、结构体或枚举都可以遵守协议，并为协议定义的这些要求提供具体的实现。任何满足某协议要求的类型都可以被认为该类型遵守了此协议。

In addition to specifying requirements that conforming types must implement, you can extend a protocol to implement some of these requirements or to implement additional functionality that conforming types can take advantage of.

除了遵守协议的类型必须实现的要求外，还可以对协议进行扩展，通过扩展来实现一部分要求或者实现一些附加功能，这样遵循协议的类型就能够使用这些功能。

## Protocol Syntax（协议语法）

You define protocols in a very similar way to classes, structures,and enumerations:

协议的定义方式与类、结构体和枚举的定义非常相似：

```
protocol SomeProtocol {
    // protocol definition goes here
}
```

Custom types state that they adopt a particular protocol by placing the protocol’s name after the type’s name, separated by a colon, as part of their definition. Multiple protocols can be listed, and are separated by commas:

要让自定义类型遵循某个协议，在定义类型时，需要在类型名称后加上协议名称，中间以冒号（:）分隔。遵循多个协议时，各协议之间用逗号（,）分隔：

```
struct SomeStructure: FirstProtocol, AnotherProtocol {
    // structure definition goes here
}
```
