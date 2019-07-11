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

if a class has a superclass, list the superclass name before any protocols it adopts, followed by a comma:

当一个拥有父类的类在遵循协议时，应该将父类名放在协议名之前，并以逗号分隔：

```
class SomeClass: SomeSuperclass, FirstProtocol, AnotherProtocol {
    // class definition goes here
}
```

## Property Requirements（协议要求）

A protocol can require any conforming type to provide an instance property or type property with a particular name and type. The protocol doesn't specify whether the property should be a stored property or a computed property——it only specifies the required property name and type. The protocol specifies whether each property must be gettable or gettable and settable.

协议要求遵守了协议的类提供一个指定名称和类型的实例属性或者类型属性。协议不指定属性是存储属性还是计算属性，它只指定属性的名称和类型。协议还将指定每条属性到底是可读的还是可读可写的。

If a protocol requires a property to be gettable and settable, that property requirement can't be fulfilled by a constant stored property or a read-only computed property. If the protocol only requires a property to be gettable, the requirement can be satisfied by any kind of property, and it's valid for the property to be also settable if this is useful for your own code.

如果协议要求属性是可读可写的，那么该属性不能是常量属性或只读的计算属性。如果协议只要求属性是可读的，那么该属性不仅可以是可读的，如果代码需要的话，还可以是可写的。

Property requirements are always declared as variable properties, prefixed with the var keyword. Gettable and settable properties are indicated by writing { get set } after their type declaration, and gettable properties are indicated by writing { get }.

协议总是用 var 关键字来声明变量属性。在类型声明后加上 { get set } 来表示属性是可读可写的，在类型声明后加上 { get } 来表示属性是可读的：

```
protocol SomeProtocol {
    var mustBeSettable: Int { get set }
    var doesNotNeedToBeSettable: Int { get }
}
```

Always prefix type property requirements with the static keyword when you define them in a protocol. This rule pertains even though type property requirements can be prefixed with the class or static keyword when implemented by a class:

在协议中定义类型属性时，总是使用 static 关键字作为前缀。当类型遵循协议时，除了 static 关键字，还可以使用 class 关键字来声明类型属性：

```
protocol AnotherProtocol {
    static var someTypeProperty: Int { get set }
}
```

Here's an example of a protocol with a single instance property requirement:

如下所示，这是一个只含有一个实例属性要求的协议：

```
protocol FullyNamed {
    var fullName: String { get }
}
```

The FullyNamed protocol requires a conforming type to provide a fully qualified name. The protocol doesn’t specify anything else about the nature of the conforming type—it only specifies that the type must be able to provide a full name for itself. The protocol states that any FullyNamed type must have a gettable instance property called fullName, which is of type String.

FullyNamed 协议除了要求遵循协议的类型提供 fullName 属性外，并没有其他特别的要求。这个协议表示，任何遵循 FullyNamed 协议的类型，都必须有一个可读的 String 类型的实例属性 fullName。

Here's example of a simple structure that adopts and conforms to the FullyNamed protocol:

下面是一个遵循 FullyNamed 协议的简单结构体：

```
struct Person: FullyNamed {
    var fullName: String
}
let john = Person(fullName: "John Appleseed")
```
This example defines a structure called Person, which represents a specific named person. It states that it adopts the FullyNamed protocol as part of the first line of its definition.

此示例定义了一个名为Person的结构体，此结构体表示一个具有名字的人。从第一行代码可知，它遵循了 FullyNamed 协议。

Each instance of Person has a single stored property called fullName, which is of type String. This matches the single requirement of the FullNamed protocol, and means that Person has correctly conformed to the protocol.(Swift reports an error at compile-time if a protocol requirement is not fulfilled.)

Person 结构体的每一个实例都有一个 String 类型的存储型属性 fullName。这正好满足了 FullyNamed 协议的要求，也就意味着 Person 结构体正确地符合了协议。（如果协议要求未被完全满足，在编译时会报错。）

Here's a more complex class, which also adopts and conforms to the FullyNamed protocol:

```
class Starship: FullyNamed {
    var prefix: String?
    var name: String
    init(name: String, prefix: String? = nil) {
        self.name = name
        self.prefix = prefix
    }
    var fullName: String {
        return (prefix != nil ? prefix! + "" : "") + name
    }
}

var ncc1701 = Starship(name: "Enterprise", prefix: "USS")
```

This class implements the fullName property requirement as a computed read-only property for a starship. Each Starship class instance stores a mandatory name and an optional prefix. The fullName property uses the prefix value if it exists, and prepends it to the beginning of name to create a full name for the starship.

Starship 类把 fullName 作为只读的计算属性来实现。每一个 Starship 类的实例都有一个名为 name 的非可选属性和一个名为 prefix 的可选属性。 当 prefix 存在时，计算属性 fullName 会将 prefix 前置到 name 之前，从而得到一个带有 prefix 的 fullName。


## Method Requirements(方法要求)

Protocols can require specific instance methods and type methods to be implemented by conforming types. These methods are written as part of the protocol's definition in exactly the same way as for normal instance and type methods, but without curly braces or a method body. Default values, however, can’t be specified for method parameters within a protocol’s definition.

协议可以要求遵循协议的类型实现某些指定的实例方法或类方法。这些方法作为协议的一部分，像普通实例方法和类方法一样放在协议的定义中，但是不需要大括号和方法体。可以在协议中定义具有可变参数的方法，和普通方法的定义方式相同。但是，不支持为协议中的方法提供默认参数。

As with type property requirements, you always prefix type method requirements with the static keyword when they’re defined in a protocol. This is true even though type method requirements are prefixed with the class or static keyword when implemented by a class:

正如属性要求中所述，在协议中定义类方法的时候，总是使用 static 关键字作为前缀。即使在类实现时，类方法要求使用 class 或 static 作为关键字前缀，前面的规则仍然适用：

```
protocol SomeProtocol {
    static func someTypeMethod()
}
```

The following example defines a protocol with a single instance method requirement:

下面的例子定义了一个只含有一个实例方法的协议：

```
protocol SomeProtocol {
    static func someTypeMethod()
}
```

This protocol, RandomNumberGenerator, requires any conforming type to have an instance method called random, which returns a Double value whenever it’s called. Although it’s not specified as part of the protocol, it’s assumed that this value will be a number from 0.0 up to (but not including) 1.0.

RandomNumberGenerator 协议要求遵循协议的类型必须拥有一个名为 random， 返回值类型为 Double 的实例方法。尽管这里并未指明，但是我们假设返回值是从 0.0 到（但不包括）1.0。

The RandomNumberGenerator protocol doesn’t make any assumptions about how each random number will be generated—it simply requires the generator to provide a standard way to generate a new random number.

RandomNumberGenerator 协议并不关心每一个随机数是怎样生成的，它只要求必须提供一个随机数生成器。

Here’s an implementation of a class that adopts and conforms to the RandomNumberGenerator protocol. This class implements a pseudorandom number generator algorithm known as a linear congruential generator:

如下所示，下边是一个遵循并符合 RandomNumberGenerator 协议的类。该类实现了一个叫做 线性同余生成器（linear congruential generator） 的伪随机数算法。

```
class LinearCongruentialGenerator: RandomNumberGenerator {
    var lastRandom = 42.0
    let m = 139968.0
    let a = 3877.0
    let c = 29573.0
    func random() -> Double {
        lastRandom = ((lastRandom * a + c).truncatingRemainder(dividingBy:m))
        return lastRandom / m
    }
}
let generator = LinearCongruentialGenerator()
print("Here's a random number: \(generator.random())")
// 打印 “Here's a random number: 0.3746499199817101”
print("And another one: \(generator.random())")
// 打印 “And another one: 0.729023776863283”
```

