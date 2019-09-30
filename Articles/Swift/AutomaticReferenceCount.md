# Automatic Reference Counting

Swift uses Automatic Reference Counting (ARC) to track and manage your app’s memory usage. In most cases, this means that memory management “just works” in Swift, and you do not need to think about memory management yourself. ARC automatically frees up the memory used by class instances when those instances are no longer needed.

Swift 使用自动引用计数（ARC）机制来跟踪和管理你的应用程序的内存。通常情况下，Swift 内存管理机制会一直起作用，你无须自己来考虑内存的管理。ARC 会在类的实例不再被使用时，自动释放其占用的内存。

However, in a few cases ARC requires more information about the relationships between parts of your code in order to manage memory for you. This chapter describes those situations and shows how you enable ARC to manage all of your app’s memory. Using ARC in Swift is very similar to the approach described in Transitioning to ARC Release Notes for using ARC with Objective-C.

然而，在少数情况下，为了能帮助你管理内存，ARC 需要更多的，代码之间关系的信息。本章描述了这些情况，并且为你示范怎样才能使 ARC 来管理你的应用程序的所有内存。在 Swift 使用 ARC 与在 Obejctive-C 中使用 ARC 非常类似，具体请参考 过渡到 ARC 的发布说明。

Reference counting applies only to instances of classes. Structures and enumerations are value types, not reference types, and are not stored and passed by reference.

引用计数仅仅应用于类的实例。结构体和枚举类型是值类型，不是引用类型，也不是通过引用的方式存储和传递。

## How ARC Works（自动引用计数的工作机制）

Every time you create a new instance of a class, ARC allocates a chunk of memory to store information about that instance. This memory holds information about the type of the instance, together with the values of any stored properties associated with that instance.

当你每次创建一个类的新的实例的时候，ARC 会分配一块内存来储存该实例信息。内存中会包含实例的类型信息，以及与这个实例所有相关的存储型属性的值。

Additionally, when an instance is no longer needed, ARC frees up the memory used by that instance so that the memory can be used for other purposes instead. This ensures that class instances do not take up space in memory when they are no longer needed.

此外，当实例不再被使用时，ARC 释放实例所占用的内存，并让释放的内存能挪作他用。这确保了不再被使用的实例，不会一直占用内存空间。

However, if ARC were to deallocate an instance that was still in use, it would no longer be possible to access that instance’s properties, or call that instance’s methods. Indeed, if you tried to access the instance, your app would most likely crash.

然而，当 ARC 收回和释放了正在被使用中的实例，该实例的属性和方法将不能再被访问和调用。实际上，如果你试图访问这个实例，你的应用程序很可能会崩溃。

To make sure that instances don’t disappear while they are still needed, ARC tracks how many properties, constants(常量), and variables are currently referring to each class instance. ARC will not deallocate an instance as long as at least one active reference to that instance still exists.

为了确保使用中的实例不会被销毁，ARC 会跟踪和计算每一个实例正在被多少属性，常量和变量所引用。哪怕实例的引用数为 1，ARC 都不会销毁这个实例。

To make this possible, whenever you assign a class instance to a property, constant, or variable, that property, constant, or variable makes a strong reference to the instance. The reference is called a “strong” reference because it keeps a firm hold on that instance, and does not allow it to be deallocated for as long as that strong reference remains.

为了使上述成为可能，无论你将实例赋值给属性、常量或变量，它们都会创建此实例的强引用。之所以称之为“强”引用，是因为它会将实例牢牢地保持住，只要强引用还在，实例是不允许被销毁的。

## ARC in Action（自动引用计数实践）

Here’s an example of how Automatic Reference Counting works. This example starts with a simple class called Person, which defines a stored constant property called name:

下面的例子展示了自动引用计数的工作机制。例子以一个简单的 Person 类开始，并定义了一个叫 name 的常量属性：

```
class Person {
    let name: String
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    deinit {
        print("\(name) is being deinitialized")
    }
}
```

The Person class has an initializer that sets the instance’s name property and prints a message to indicate that initialization is underway. The Person class also has a deinitializer that prints a message when an instance of the class is deallocated.

Person 类有一个构造器，此构造器为实例的 name 属性赋值，并打印一条消息以表明初始化过程生效。Person 类也拥有一个析构器，这个析构器会在实例被销毁时打印一条消息。

The next code snippet defines three variables of type Person?, which are used to set up multiple references to a new Person instance in subsequent code snippets. Because these variables are of an optional type (Person?, not Person), they are automatically initialized with a value of nil, and do not currently reference a Person instance.

接下来的代码片段定义了三个类型为 Person? 的变量，用来按照代码片段中的顺序，为新的 Person 实例建立多个引用。由于这些变量是被定义为可选类型（Person?，而不是 Person），它们的值会被自动初始化为 nil，目前还不会引用到 Person 类的实例。

```
var reference1: Person?
var reference2: Person?
var reference3: Person?
```

You can now create a new Person instance and assign it to one of these three variables:

现在你可以创建 Person 类的新实例，并且将它赋值给三个变量中的一个：

```
reference1 = Person(name: "John Appleseed")
// Prints "John Appleseed is being initialized"
```

Note that the message "John Appleseed is being initialized" is printed at the point that you call the Person class’s initializer. This confirms that initialization has taken place.

应当注意到当你调用 Person 类的构造器的时候，"John Appleseed is being initialized" 会被打印出来。由此可以确定构造器被执行。

Because the new Person instance has been assigned to the reference1 variable, there is now a strong reference from reference1 to the new Person instance. Because there is at least one strong reference, ARC makes sure that this Person is kept in memory and is not deallocated.

由于 Person 类的新实例被赋值给了 reference1 变量，所以 reference1 到 Person 类的新实例之间建立了一个强引用。正是因为这一个强引用，ARC 会保证 Person 实例被保持在内存中不被销毁。

If you assign the same Person instance to two more variables, two more strong references to that instance are established:

如果你将同一个 Person 实例也赋值给其他两个变量，该实例又会多出两个强引用:

```
reference2 = reference1
reference3 = reference1
```

There are now three strong references to this single Person instance.

现在这一个 Person 实例已经有三个强引用了。

If you break two of these strong references (including the original reference) by assigning nil to two of the variables, a single strong reference remains, and the Person instance is not deallocated:

如果你通过给其中两个变量赋值 nil 的方式断开两个强引用（包括最先的那个强引用），只留下一个强引用，Person 实例不会被销毁：

```
reference1 = nil
reference2 = nil
```

ARC does not deallocate the Person instance until the third and final strong reference is broken, at which point it’s clear that you are no longer using the Person instance:

在你清楚地表明不再使用这个 Person 实例时，即第三个也就是最后一个强引用被断开时，ARC 会销毁它：

```
reference3 = nil
// Prints "John Appleseed is being deinitialized"
```











