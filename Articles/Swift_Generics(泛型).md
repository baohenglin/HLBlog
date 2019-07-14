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
