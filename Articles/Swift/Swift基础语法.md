## 1. let 声明常量 var声明变量

```
let myConstant = 42
var myVariable = 42
myVariable = 50
```

编译器会自动推断出 myVariable 是一个整数类型，因为它的初始值是整数。如果初始值没有提供足够的信息（或者没有初始值），那么就需要在变量后面声明类型，用冒号分割。

**1.1 值永远不会被隐式转化为其他类型。如果需要把一个值转换成其他类型，请显示转换**。

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

**1.2 使用三个双引号（"""）来包含多行字符串内容**。每行行首的缩进会被去除，直到和结尾引号的缩进相匹配。

```
let quotation = """
I said "I have \(apples) apples."
And then I said "I have \(apples + oranges) pieces of fruit."
"""
```

**1.3 使用方括号 [] 来创建数组和字典，并使用下标或者键（key）来访问元素**。最后一个元素后面允许有个逗号。

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

**1.4 使用初始化语法来创建一个空数组或者空字典**。

```
let emptyArray = [String]()
let emptyDictionary = [String: Float]()
```

如果类型信息可以被推断出来，你可以用 [] 和 [:] 来创建空数组和空字典。比如，在给变量赋新值或者给函数传参数的时候。

```
shoppingList = []
occupations = [:]
```

## 2 控制流

**2.1使用 if 和 switch 来进行条件操作，使用 for-in、 while 和 repeat-while 来进行循环**。包裹条件和循环变量的括号可以省略，但是语句体的大括号是必须的。

```
let individualScores = [75, 43, 103, 87, 12]
var teamScore = 0
for score in individualScores {
    if score > 50 {
        teamScore += 3
    } else {
        teamScore += 1
    }
}
print(teamScore)
```

**在 if 语句中，条件必须是一个布尔表达式。** 这意味着像 if score { ... } 这样的代码将报错，而不会隐形地与 0 做对比。

可以一起使用 if 和 let 一起来处理值缺失的情况。这些值可由可选值来代表。一个可选的值是一个具体的值或者是 nil 以表示值缺失。在类型后面加一个问号（?）来标记这个变量的值是可选的。

```
var optionalString: String? = "Hello"
print(optionalString == nil)

var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)"
}
```

如果变量的可选值是 nil，条件会判断为 false，大括号中的代码会被跳过。如果不是 nil，会将值解包并赋给 let 后面的常量，这样代码块中就可以使用这个值了。

另一种处理可选值的方法是**通过使用 ?? 操作符** 来提供一个默认值。如果可选值缺失的话，可以使用默认值来代替。

```
let nickName: String? = nil
let fullName: String = "John Appleseed"
let informalGreeting = "Hi \(nickName ?? fullName)"
```

**2.2 switch 支持任意类型的数据以及各种比较操作——不仅仅是整数以及测试相等**

```
let vegetable = "red pepper"
switch vegetable {
case "celery":
    print("Add some raisins and make ants on a log.")
case "cucumber", "watercress":
    print("That would make a good tea sandwich.")
case let x where x.hasSuffix("pepper"):
    print("Is it a spicy \(x)?")
default:
    print("Everything tastes good in soup.")
}
```

let 在上述例子的等式中是如何使用的，它将匹配等式的值赋给常量 x。

运行 switch 中匹配到的 case 语句之后，程序会退出 switch 语句，并不会继续向下运行，所以不需要在每个子句结尾写 break。

**2.3 使用 for-in 来遍历字典**

使用 for-in 来遍历字典，需要一对变量来表示每个键值对。字典是一个无序的集合，所以他们的键和值以任意顺序迭代结束。

```
let interestingNumbers = [
    "Prime": [2, 3, 5, 7, 11, 13],
    "Fibonacci": [1, 1, 2, 3, 5, 8],
    "Square": [1, 4, 9, 16, 25],
]
var largest = 0
for (kind, numbers) in interestingNumbers {
    for number in numbers {
        if number > largest {
            largest = number
        }
    }
}
print(largest)
```

**2.4 while循环**

使用 while 来重复运行一段代码直到条件改变。循环条件也可以在结尾，保证能至少循环一次。

```
var n = 2
while n < 100 {
    n *= 2
}
print(n)

var m = 2
repeat {
    m *= 2
} while m < 100
print(m)
```

**2.5 在循环中使用 ..< 来表示下标范围**

```
var total = 0
for i in 0..<4 {
    total += i
}
print(total)
//0..<4表示 0 <= x < 4
//0...4表示 0 <= x <= 4
```

需要注意的是：使用 ..< 创建的范围不包含上界，如果想包含的话需要使用 ...。

## 3.函数和闭包

**3.1 函数**

使用 func 来声明一个函数，使用名字和参数来调用函数。使用 -> 来指定函数返回值的类型。

```
func greet(person: String, day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet(person:"Bob", day: "Tuesday")
```

默认情况下，函数使用它们的参数名称作为它们参数的标签，在参数名称前可以自定义参数标签，或者使用 _ 表示不使用参数标签。

```
func greet(_ person: String, on day: String) -> String {
    return "Hello \(person), today is \(day)."
}
greet("John", on: "Wednesday")
```

**3.1.1 使用元组来生成复合值**

使用元组来生成复合值，比如让一个函数返回多个值。该元组的元素可以用名称或数字来获取。

```
func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
    var min = scores[0]
    var max = scores[0]
    var sum = 0

    for score in scores {
        if score > max {
            max = score
        } else if score < min {
            min = score
        }
        sum += score
    }

    return (min, max, sum)
}
let statistics = calculateStatistics(scores:[5, 3, 100, 3, 9])
print(statistics.sum)
print(statistics.2)
```

**3.1.2 函数可以嵌套**

函数可以嵌套。被嵌套的函数可以访问外侧函数的变量，你可以使用嵌套函数来重构一个太长或者太复杂的函数。

```
func returnFifteen() -> Int {
    var y = 10
    func add() {
        y += 5
    }
    add()
    return y
}
returnFifteen()
```

**3.1.3 函数是第一等类型，这意味着函数可以作为另一个函数的返回值。**

```
func makeIncrementer() -> ((Int) -> Int) {
    func addOne(number: Int) -> Int {
        return 1 + number
    }
    return addOne
}
var increment = makeIncrementer()
print(increment(7))
// 8
```

**3.1.4 函数也可以当做参数传入另一个函数。**

```
func hasAnyMatches(list: [Int], condition: (Int) -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}
func lessThanTen(number: Int) -> Bool {
    return number < 10
}
var numbers = [20, 19, 7, 12]
hasAnyMatches(list: numbers, condition: lessThanTen)
```

**3.2 闭包**

函数实际上是一种特殊的闭包:它是一段能之后被调取的代码。闭包中的代码能访问闭包作用域中的变量和函数，即使闭包是在一个不同的作用域被执行的——你已经在嵌套函数的例子中看过了。你可以使用 {} 来创建一个匿名闭包。使用 in 将参数和返回值类型的声明与闭包函数体进行分离。

```
numbers.map({
    (number: Int) -> Int in
    let result = 3 * number
    return result
})
```

有很多种创建更简洁的闭包的方法。如果一个闭包的类型已知，比如作为一个代理的回调，你可以忽略参数，返回值，甚至两个都忽略。单个语句闭包会把它语句的值当做结果返回。

```
let mappedNumbers = numbers.map({ number in 3 * number })
print(mappedNumbers)
```

你可以通过参数位置而不是参数名字来引用参数——这个方法在非常短的闭包中非常有用。当一个闭包作为最后一个参数传给一个函数的时候，它可以直接跟在圆括号后面。当一个闭包是传给函数的唯一参数，你可以完全忽略圆括号。

```
let sortedNumbers = numbers.sorted { $0 > $1 }
print(sortedNumbers)
```



