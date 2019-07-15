


## 10. 关联类型：

 定义一个协议时，声明一个或多个关联类型作为协议定义的一部分将会非常有用。关联类型为协议中的某个类型提供了一个占位符名称，其代表的实际类型在协议被遵循时才会被指定。关联类型通过 associatedtype 关键字来指定。
 
定义了一个 Container 协议，该协议定义了一个关联类型 Item：

```
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

 Container 协议定义了三个任何遵循该协议的类型（即容器）必须提供的功能：
 
 (1)必须可以通过 append(_:) 方法添加一个新元素到容器里。
 
 (2)必须可以通过 count 属性获取容器中元素的数量，并返回一个 Int 值。
 
 (3)必须可以通过索引值类型为 Int 的下标检索到容器中的每一个元素。
 
 该协议没有指定容器中元素该如何存储以及元素类型。该协议只指定了任何遵从 Container 协议的类型必须提供的三个功能。遵从协议的类型在满足这三个条件的情况下，也可以提供其他额外的功能。
 
 任何遵从 Container 协议的类型必须能够指定其存储的元素的类型。具体来说，它必须确保添加到容器内的元素以及下标返回的元素类型是正确的。
 
 为了定义这些条件，Container 协议需要在不知道容器中元素的具体类型的情况下引用这种类型。Container 协议需要指定任何通过 append(_:) 方法添加到容器中的元素和容器内的元素是相同类型，并且通过容器下标返回的元素的类型也是这种类型。
 
 为此，Container 协议声明了一个关联类型 Item，写作 associatedtype Item。协议没有定义 Item 是什么，这个信息留给遵从协议的类型来提供。尽管如此，Item 别名提供了一种方式来引用 Container 中元素的类型，并将之用于 append(_:) 方法和下标，从而保证任何 Container 的行为都能如预期。

```
struct IntStack: Container {
    // IntStack 的原始实现部分
    var items = [Int]()
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    
    // Container 协议的实现部分
    //IntStack 在实现 Container 的要求时，指定 Item 为 Int 类型，即 typealias Item = Int，从而将 Container 协议中抽象的 Item 类型转换为具体的 Int 类型。
    typealias Item = Int
    mutating func append(_ item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}
```

由于 Swift 的类型推断，实际上在 IntStack 的定义中不需要声明 Item 为 Int。因为 IntStack 符合 Container 协议的所有要求，Swift 只需通过 append(_:) 方法的 item 参数类型和下标返回值的类型，就可以推断出 Item 的具体类型。事实上，如果你在上面的代码中删除了 typealias Item = Int 这一行，一切也可正常工作，因为 Swift 清楚地知道 Item 应该是哪种类型。

```
struct Stack<Element>: Container {
    // Stack<Element> 的原始实现部分
    var items = [Element]()
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
    // Container 协议的实现部分
    mutating func append(_ item: Element) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Element {
        return items[i]
    }
}
```

此时，占位类型参数 Element 被用作 append(_:) 方法的 item 参数和下标的返回类型。Swift 可以据此推断出 Element 的类型即是 Item 的类型。

## 11. 扩展现有类型来指定关联类型

 Swift 的 Array 类型已经提供 append(_:) 方法，count 属性，以及带有 Int 索引的下标来检索其元素。这三个功能都符合 Container 协议的要求，也就意味着你只需声明 Array 遵循Container 协议，就可以扩展 Array，使其遵从 Container 协议。你可以通过一个空扩展来实现这点，正如通过扩展采纳协议中的描述：
 
 ```
 extension Array: Container {}
 ```
 
 Array 的 append(_:) 方法和下标确保了 Swift 可以推断出 Item 具体类型。定义了这个扩展后，你可以将任意 Array 当作 Container 来使用。


## 12.给关联类型添加约束

你可以在协议里给关联类型添加约束来要求遵循协议的类型满足约束。例如，下面的代码定义了 Container 协议， 要求关联类型 Item 必须遵循 Equatable 协议：

```
protocol Container {
    associatedtype Item: Equatable
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

要遵守 Container 协议，Item 类型也必须遵守 Equatable 协议。

## 13. 在关联类型里使用协议

协议可以作为它自身的要求出现。例如，有一个协议细化了 Container 协议，添加了一个suffix(_:) 方法。suffix(_:) 方法返回容器中从后往前给定数量的元素，并把它们存储在一个 Suffix 类型的实例里。
```
protocol SuffixableContainer: Container {
    associatedtype Suffix: SuffixableContainer where Suffix.Item == Item
    func suffix(_ size: Int) -> Suffix
}
```

在这个协议里，Suffix 是一个关联类型，就像上边例子中 Container 的 Item 类型一样。Suffix 拥有两个约束：它必须遵循 SuffixableContainer 协议（就是当前定义的协议），以及它的 Item 类型必须是和容器里的 Item 类型相同。Item 的约束是一个 where 分句，

```
extension Stack: SuffixableContainer {
    func suffix(_ size: Int) -> Stack {
        var result = Stack()
        for index in (count-size)..<count {
            result.append(self[index])
        }
        return result
    }
}
var stackOfInts = Stack<Int>()
stackOfInts.append(10)
stackOfInts.append(20)
stackOfInts.append(30)
let suffix = stackOfInts.suffix(2)
print(suffix)
```

在上面的例子中，Suffix 是 Stack 的关联类型，也是 Stack ，所以 Stack 的后缀运算返回另一个 Stack 。另外，遵循 SuffixableContainer 的类型可以拥有一个与它自己不同的 Suffix 类型——也就是说后缀运算可以返回不同的类型。比如说，这里有一个非泛型 IntStack 类型的扩展，它遵循了 SuffixableContainer 协议，使用 Stack<Int> 作为它的后缀类型而不是 IntStack：
 
```
extension IntStack: SuffixableContainer {
    func suffix(_ size: Int) -> Stack<Int> {
        var result = Stack<Int>()
        for index in (count-size)..<count {
            result.append(self[index])
        }
        return result
    }
    // 推断 suffix 结果是 Stack<Int>。
}
```

 ## 14. 泛型where语句
 
 类型约束 让你能够为泛型函数、下标、类型的类型参数定义一些强制要求。
 对关联类型添加约束通常是非常有用的。你可以通过定义一个泛型 where 子句来实现。通过泛型 where 子句让关联类型遵从某个特定的协议，以及某个特定的类型参数和关联类型必须类型相同。你可以通过将 where 关键字紧跟在类型参数列表后面来定义 where 子句，where 子句后跟一个或者多个针对关联类型的约束，以及一个或多个类型参数和关联类型间的相等关系。你可以在函数体或者类型的大括号之前添加 where 子句。
 
下面的例子定义了一个名为 allItemsMatch 的泛型函数，用来检查两个 Container 实例是否包含相同顺序的相同元素。如果所有的元素能够匹配，那么返回 true，否则返回 false。

被检查的两个 Container 可以不是相同类型的容器（虽然它们可以相同），但它们必须拥有相同类型的元素。这个要求通过一个类型约束以及一个 where 子句来表示：

```
func allItemsMatch<C1: Container, C2: Container>
    (_ someContainer: C1, _ anotherContainer: C2) -> Bool
    where C1.Item == C2.Item, C1.Item: Equatable {
        //allItemsMatch(_:_:) 函数首先检查两个容器元素个数是否相同，如果元素个数不同，那么一定不匹配，函数就会返回 false。
        // 检查两个容器含有相同数量的元素
        if someContainer.count != anotherContainer.count {
            return false
        }
        //通过 for-in 循环和半闭区间操作符（..<）来迭代每个元素，检查 someContainer 中的元素是否不等于 anotherContainer 中的对应元素。如果两个元素不相等，那么两个容器不匹配，函数返回 false。
        // 检查每一对元素是否相等
        for i in 0..<someContainer.count {
            if someContainer[i] != anotherContainer[i] {
                return false
            }
        }
        //如果循环体结束后未发现任何不匹配的情况，表明两个容器匹配，函数返回 true
        // 所有元素都匹配，返回 true
        return true
}
```

这个函数接受 someContainer 和 anotherContainer 两个参数。参数 someContainer 的类型为 C1，参数 anotherContainer 的类型为 C2。C1 和 C2 是容器的两个占位类型参数，函数被调用时才能确定它们的具体类型。

 这个函数的类型参数列表还定义了对两个类型参数的要求：
 
 C1 必须符合 Container 协议（写作 C1: Container）;
 
 C2 必须符合 Container 协议（写作 C2: Container）;
 
 C1 的 Item 必须和 C2 的 Item 类型相同（写作 C1.Item == C2.Item）;
 
 C1 的 Item 必须符合 Equatable 协议（写作 C1.Item: Equatable）
 
 前两个要求定义在函数的类型形式参数列表里，后两个要求定义在了函数的泛型 where 分句中。
 
 这些要求意味着：
 
 someContainer 是一个 C1 类型的容器。anotherContainer 是一个 C2 类型的容器。someContainer 和 anotherContainer 包含相同类型的元素。
 someContainer 中的元素可以通过不等于操作符（!=）来检查它们是否相同。
 
 第三个和第四个要求结合起来意味着 anotherContainer 中的元素也可以通过 != 操作符来比较，因为它们和 someContainer 中的元素类型相同。
 
 这些要求让 allItemsMatch(_:_:) 函数能够比较两个容器，即使它们的容器类型不同。

```
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")

var arrayOfStrings = ["uno", "dos", "tres"]
if allItemsMatch(stackOfStrings, arrayOfStrings) {
    print("All items match.")
} else {
    print("Not all items match.")
}
```

上面的例子创建 Stack 实例来存储 String 值，然后将三个字符串压栈。这个例子还通过数组字面量创建了一个 Array 实例，数组中包含同栈中一样的三个字符串。即使栈和数组是不同的类型，但它们都遵从 Container 协议，而且它们都包含相同类型的值。因此你可以用这两个容器作为参数来调用 allItemsMatch(_:_:) 函数。在上面的例子中，allItemsMatch(_:_:) 函数正确地显示了这两个容器中的所有元素都是相互匹配的


## 15. 具有泛型Where子句的扩展：

```
extension Stack where Element: Equatable {
    func isTop(_ item: Element) -> Bool {
        guard let topItem = items.last else {
            return false
        }
        return topItem == item
    }
}
```

这个新的 isTop(_:) 方法首先检查这个栈是不是空的，然后比较给定的元素与栈顶部的元素。如果你尝试不用泛型 where 子句，会有一个问题：在 isTop(_:) 里面使用了 == 运算符，但是 Stack 的定义没有要求它的元素是符合 Equatable 协议的，所以使用 == 运算符导致编译时错误。使用泛型 where 子句可以为扩展添加新的条件，因此只有当栈中的元素符合 Equatable 协议时，扩展才会添加 isTop(_:) 方法。

以下是 isTop(_:) 方法的调用方式：

```
if stackOfStrings.isTop("tres") {
    print("Top element is tres.")
} else {
    print("Top element is something else.")
}
// 打印“Top element is tres.”
```

如果尝试在其元素不符合 Equatable 协议的栈上调用 isTop(_:) 方法，则会收到编译时错误:

```
struct NotEquatable { }
var notEquatableStack = Stack<NotEquatable>()
let notEquatableValue = NotEquatable()
notEquatableStack.push(notEquatableValue)
notEquatableStack.isTop(notEquatableValue)  // 报错
```

你可以使用泛型 where 子句去扩展一个协议。基于以前的示例，下面的示例扩展了 Container 协议，添加一个 startsWith(_:) 方法。

```
extension Container where Item: Equatable {
    func startsWith(_ item: Item) -> Bool {
        return count >= 1 && self[0] == item
    }
}
```

这个 startsWith(_:) 方法首先确保容器至少有一个元素，然后检查容器中的第一个元素是否与给定的元素相等。任何符合 Container 协议的类型都可以使用这个新的 startsWith(_:) 方法，包括上面使用的栈和数组，只要容器的元素是符合 Equatable 协议的。

```
if [9, 9, 9].startsWith(42) {
    print("Starts with 42.")
} else {
    print("Starts with something else.")
}
// 打印“Starts with something else.”


extension Container where Item == Double {
    func average() -> Double {
        var sum = 0.0
        for index in 0..<count {
            sum += self[index]
        }
        return sum / Double(count)
    }
}
print([1260.0, 1200.0, 98.6, 37.0].average())
//// 打印“648.9”
```

此示例将一个 average() 方法添加到 Item 类型为 Double 的容器中。此方法遍历容器中的元素将其累加，并除以容器的数量计算平均值。它将数量从 Int 转换为 Double 确保能够进行浮点除法。

就像可以在其他地方写泛型 where 子句一样，你可以在一个泛型 where 子句中包含多个条件作为扩展的一部分。用逗号分隔列表中的每个条件。


## 16. 泛型下标：

 下标可以是泛型，它们能够包含泛型 where 子句。你可以在 subscript 后用尖括号来写占位符类型，你还可以在下标代码块花括号前写 where 子句。例如：

```
extension Container {
    subscript<Indices: Sequence>(indices: Indices) -> [Item]
        where Indices.Iterator.Element == Int {
            var result = [Item]()
            for index in indices {
                result.append(self[index])
            }
            return result
    }
}
```

这个 Container 协议的扩展添加了一个下标方法，接收一个索引的集合，返回每一个索引所在的值的数组。这个泛型下标的约束如下：
 
 在尖括号中的泛型参数 Indices，必须是符合标准库中的 Sequence 协议的类型。
 
 下标使用的单一的参数，indices，必须是 Indices 的实例。
 
 泛型 where 子句要求 Sequence（Indices）的迭代器，其所有的元素都是 Int 类型。这样就能确保在序列（Sequence）中的索引和容器（Container）里面的索引类型是一致的。

