## 1. let 声明常量 var声明变量

```
let myConstant = 42
var myVariable = 42
myVariable = 50
```

编译器会自动推断出 myVariable 是一个整数类型，因为它的初始值是整数。如果初始值没有提供足够的信息（或者没有初始值），那么就需要在变量后面声明类型，用冒号分割。

**值永远不会被隐式转化为其他类型。如果需要把一个值转换成其他类型，请显示转换**。

```
let label = "The width is"
let width = 94
let widthLabel = label + String(width)
```

另一种更简单的把值转换成字符串的方法：**把值写在括号中，并且在括号之前写一个反斜杠(\)**。

```
let apples = 3
let oranges = 5
let appleSummary = "I have \(apples) apples."
let fruitSummary = "I have \(apples + oranges) pieces of fruit."
```

**使用三个双引号（"""）来包含多行字符串内容**。每行行首的缩进会被去除，直到和结尾引号的缩进相匹配。

```
let quotation = """
I said "I have \(apples) apples."
And then I said "I have \(apples + oranges) pieces of fruit."
"""
```

**使用方括号 [] 来创建数组和字典，并使用下标或者键（key）来访问元素**。最后一个元素后面允许有个逗号。

```
var shoppingList = ["catfish", "water", "tulips", "blue paint"]
shoppingList[1] = "bottle of water"

var occupations = [
    "Malcolm": "Captain",
    "Kaylee": "Mechanic",
]
occupations["Jayne"] = "Public Relations"
```

数组在添加元素时会自动变大。

```
shoppingList.append("blue paint")
```

**使用初始化语法来创建一个空数组或者空字典**。

```
let emptyArray = [String]()
let emptyDictionary = [String: Float]()
```

如果类型信息可以被推断出来，你可以用 [] 和 [:] 来创建空数组和空字典。比如，在给变量赋新值或者给函数传参数的时候。

```
shoppingList = []
occupations = [:]
```




