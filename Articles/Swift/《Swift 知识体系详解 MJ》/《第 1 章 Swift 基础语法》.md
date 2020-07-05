
![Swift知识体系图.png](https://upload-images.jianshu.io/upload_images/4164292-a9bba796612e25d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 常量

常量只能赋值 1 次，常量的值**不要求在编译时期确定，但使用之前必须赋值 1 次**。

```
//方式1
let age: Int = 20
//方式2
let age: Int
age = 20
```

常量的值**不要求在编译时期确定，但使用之前必须赋值 1 次**。

```
func getAge() -> Int {
  return 10;
}

let age = getAge()
print(age)
```

## 常见数据类型

![Swift常见数据类型.png](https://upload-images.jianshu.io/upload_images/4164292-3efc25e2b333769c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Swift 中的数据类型分为两大类：值类型（value type）和引用类型（reference type）。其中值类型包括：枚举（enum）和结构体（struct）。引用类型包括类（class）。

* 枚举：Optional
* 结构体：Bool、Int、Float、Double、Character、String、Array、Dictionary、Set。

其中 Int 整数类型又包括：Int8、Int16、Int32、Int64、UInt8、UInt16、UInt32、UInt64。在 32 bit 机器上，Int 等价于 Int32，在 64 bit 机器上，Int 等价于 Int64。

在 Swift 中将 Int 设计为 Struct 的好处，整型可以直接调用方法。比如 UInt8.max、Int16.min。

## 字面量

```
//布尔
let bool = true

//字符串
let string = "bhl"

//字符（可存储ASCII字符、Unicode字符）
let character: Character = "a"

// 整数
let intDecimal = 17 //十进制
let intBinary = 0b10001 //二进制
let intOctal = 0o21 //八进制
let intHexadecimal = 0x11 //十六进制

//数组
let array = [1, 3, 5, 7]
//字典
let dictionary = ["age" : 18,"height" : 168]
```

## 类型转化

```
let int1: UInt16 = 2_000
let int2: UInt8 = 1
let int3 = int1 + UInt16(int2)
```

字面量可以直接相加，因为数字字面量本身没有明确的类型。

```
let result = 3 + 0.14159
```

## 元组（tuple）

```
let http404Error = (404, "Not Found")
print("The status code is \(http404Error.0)")

let (statusCode, statusMessage) = http404Error
print("The status code is \(statusCode)")

//如果不想接受某个值，可以使用下划线 来忽略。
let (statusCode, _) = http404Error

let http404Error = (statusCode: 404, description: "Not Found")
print("The status code is \(http404Error.statusCode)")
```

## 流程控制

### if-else

```
let age = 4
if age >= 22 {
  print("get married")
} else if age >= 18 {
  print("Being a adult")
} else if age >= 7 {
  print("go to school")
} else {
  print("just a child")
}
```

* if 后面的条件可以省略小括号；
* 条件后面的大括号不可以省略；
* if 后面的条件只能是 Bool 类型。

###  while 

```
var num = 5
while num > 0 {
  print("num is \(num)")
  num -= 1
}
// 打印了 5 次
```

#### repeat-while 

repeat-while 相当于 C 语言的 do-while。从 Swift3 开始，去除了自增（++）、自减（--）运算符

```
var num = -1
repeat {
  print("num is \(num)")
} while num > 0 
// 打印了 1 次
```

### for 循环

* 闭区间运算符：“a...b” 表示 “a <= 取值 <= b”

```
let names = ["Anna", "Jack", "Brian", "Jamth"]
//i 默认是 let，需要时可以声明为var
for i in 0...3 {
  print(names[i])
}
//Anna Jack Brian Jamth
```

```
let names = ["Anna", "Jack", "Brian", "Jamth"]
let range = 1...3
for i in range {
  print(names[i])
}
```

```
let names = ["Anna", "Jack", "Brian", "Jamth"]
let a = 1
let b = 3
for i in a...b {
  print(names[i])
}
```

**如果 for 循环内部没有用到 i，那么可以使用下划线 _ 代替 i**

```
for _ in 1...3 {
  
}
```

* 半开区间运算符：“a..<b” 表示“a <= 取值 < b”

**for - 区间运算符用在数组上**

```
let names = ["Anna", "Jack", "Brian", "Jamth"]
for name in names[0...3] {
  print(name)
}
//Anna Jack Brian Jamth
```

* 单侧区间：让区间朝一个方向尽可能的远。

```
for name in names[2...] {
  print(name)
}
for name in names[...2] {
  print(name)
}
for name in names[..<2] {
  print(name)
}
```

```
let range = ...5
range.contains(7) //false
range.contains(-3) //true
```
**区间类型**

```
let range1: ClosedRange<Int> = 1...3
let range2: Range<Int> = 1..<3
let range3: PartialRangeThrough<Int> = ...5
```

字符、字符串也能使用区间运算符，但默认不能用在 for-in 中。如下所示：

```
//字符串
let stringRange1 = "cc"..."ff"
stringRange1.contains("cd") //true

let characterRange: ClosedRange<Character> = "\0"..."~"
characterRange.contains("G") //true
```

* 带间隔的区间值

```
let hours = 11
let hourInterval = 2
// tickMark 的取值：从 4 开始，累加 2，不超过 11
for tickMark in stride(from: 4, through: hours, by: hourInterval) {
  print(tickMark)
} // 4 6 8 10
```

### switch

```
var number = 1
switch number {
  case 1:
    print("number is 1")
    break
  case 2:
    print("number is 2")
    break
  default:
    print("number is other")
    break
}
```

* 在 case、default 后面不能写大括号 {}
* 默认可以不写 break，并不会贯穿到后面的条件；
* switch 必须要保证能处理所有情况，否则报错。
* 如果不想做任何事，加个 break 即可。

#### switch 也支持 Character、String 类型

```
// 复合条件
let string = "Jack"
switch string {
  case "Jack":
    fallthrough
  case "Rose":
    print("right person")
  default:
    break
}
// Right person 
```

等价于

let string = "Jack"
switch string {
  case "Jack",  "Rose":
    print("right person")
  default:
    break
}

**fallthrough**：用来实现贯穿效果。

 ```
var number = 1
switch number {
  case 1:
    print("number is 1")
    fallthrough
  case 2:
    print("number is 2")
    fallthrough
  default:
    print("number is other")
}
```

#### 区间匹配、元组匹配

元组匹配

```
let point = (1, 1)
switch point {
  case (0, 0):
    print("the origin")
  case (_, 0):
    print("on the x-axis")  
  case (0, _):
    print("on the y-axis")  
  case (-2...2, -2...2):
    print("inside the box")  
  default:
    print(outside of the box)
}
```

#### 值绑定

```
let point = (1, 1)
switch point {
  case (let x, 0):
    print("on the x-axis with an x value of \(x)")
  case (0, let y):
    print("on the y-axis with an x value of \(y)")  
  case let (x, y):
    print("somewhere else at (\(x), \(y))")  
}
```

#### where

```
let point = (1, -1)
switch point {
  case let (x, y) where x == y:
    print("on the line x== y")
  case let (x, y) where x == -y:
    print("on the line x== -y")
  case let (x, y) :
    print("(\(x),\(y)) is just some arbitrary point")
}
```

```
var numbers = [10, 20, -10, -20, 30, -30]
var sum = 0
// 使用 where 来过滤 num
for num in numbers where num > 0 {
  sum += num
}
print(sum)
// 60
```

## 函数

### 函数的定义

#### 完整的函数定义的格式：

```
//形参默认是 let，也只能是 let
func 函数名(参数名1: 参数类型1, 参数名2: 参数类型2,……) -> 返回值类型 {

}
```
示例如下：

```
func pi() -> Double {
  return 3.14
}
func sum(v1: Int, v2: Int) -> Int {
  return v1 + v2
}
sum(v1: 10, v2: 20)
```

#### 无返回值的函数：

```
//方式1：
func sayHello() -> Void {
  print("Hello")
}
//方式2：
func sayHello() -> () {
  
}
//方式3：
func sayHello() {
  print("Hello")
}
```

**隐式返回（Implicit Return）**：如果整个函数体是一个单一表达式，那么函数会隐式返回这个表达式。

```
func sum(v1: Int, v2: Int) -> Int {
  v1 + v2
}
sum(v1: 10, v2: 20) //30
```

#### 返回元组：实现多返回值

```
func calculate(v1: Int, v2: Int) -> (sum: Int, difference: Int, average: Int) {
  let sum = v1 + v2
  return (sum, v1 - v2, sum >> 1)
}
let result = calculate(v1: 20, v2: 10)
result.sum //30
result.difference //10
result.average //15
```

#### 函数的文档注释

#### 参数标签（Argument Label）

```
func goToWork(at time: String) {
  print("this time is \(time)")
}
goToWork(at: "08:00")
```

上面示例中 “at” 是参数标签。**也可以使用下划线“_”省略参数标签**。

```
func sum(_ v1: Int, _ v2: Int) -> Int {
  v1 + v2
}
sum(10, 20)
```

#### 默认参数值（Default Parameter Value）

参数可以有默认值。

```
func check(name: String = "nobody", age: Int, job: String = "none") {
  print("name=\(name), age=\(age), job=\(job)")
}
check(age: 15)
//name=nobody, age=15, job=none
```

C++ 的默认参数值有个限制：必须从右往左设置。由于 Swift 拥有参数标签，因此并没有此类限制。但是在省略参数标签时，需要特别注意，避免出错。

#### 可变参数（Variadic Parameter）

```
func sum(_ numbers: Int...) -> Int {
  var total = 0
  for number in numbers {
    total += number
  }
  return total
}
sum(10, 20, 30, 40)
//100
```

注意点：

* 一个函数最多只能有 1 个可变参数。
* 紧跟在可变参数后面的参数不能省略参数标签。

```
//参数 string 不能省略标签
func test(_ numbers: Int..., string: String, _ other: String) {}
test(10, 20, 30, string: "Jack", "Rose")
```

#### 输入输出参数（In-Out Parameter） 

**可以用 inout 定义一个输入输出参数：可以在函数内部修改外部实参的值**（重点）。

```
func swapValues(_ v1: inout Int, _ v2: inout Int){
  let tmp = v1
  v1 = v2
  v2 = tmp
}
var num1 = 10
var num2 = 20
swapValues(&num1, &num2)
```

注意点：

* 可变参数不能标记为 inout；
* inout 参数不能有默认值
* **inout 参数的本质是地址传递（引用传递）**

利用元组来实现两个变量交换的功能：

```
func swapValues(_ v1: inout Int, _ v2: inout Int) {
  (v1, v2) = (v2, v1)
}
```
* inout 参数只能传入可以被多次赋值的变量。

#### 常用汇编指令含义：

```
callq 函数地址  //表示函数调用
mov           //表示移动赋值
lea           //传参：地址传递
%XXX          //百分号表示XXX寄存器
(%rdi)        //表示查找 rdi 寄存器所存储的内存地址
```

#### 函数重载（Function Overload）

函数重载规则：

* 函数名相同；
* 参数个数不同 || 参数类型不同 || 参数标签不同

```
func sum(v1: Int, v2: Int) -> Int {
  v1 + v2
}

//参数个数不同
func sum(v1: Int, v2: Int, v3: Int) -> Int {
  v1 + v2 + v3
}

//参数类型不同
func sum(v1: Int, v2: Double) -> Double {
  v1 + v2
}
func sum(v1: Double, v2: Int) -> Double {
  v1 + v2
}

//参数标签不同
func sum(_ v1: Int, _ v2: Int) -> Int {
  v1 + v2
}
func sum(a: Int, b: Int) -> Int {
  a + b
}
```

函数重载注意点：

* 返回值类型与函数重载无关；
* 可变参数、省略参数标签、函数重载一起使用产生二义性时，编译器有可能会报错。

```
func sum(v1: Int, v2: Int) -> Int {
  v1 + v2
}
func sum(_ v1: Int, _ v2: Int) -> Int {
  v1 + v2
}
func sum(_ numbers: Int...) -> Int {
  var total = 0
  for number in numbers {
    total += number
  }
  return total
}
sum(10, 20)
//error: ambiguous use of "sum"
```

#### 内联函数（Inline Function）

如果开启了编译器优化（Build Settings 中 Debug模式默认没有开启优化，Release 模式默认开启优化 Optimize for Speed），编译器会自动将**某些函数**变成内联函数，将函数展开为函数体。这样就避免了函数调用时栈空间的开辟与释放等资源的消耗。

那么哪些函数不会被编译器自动优化为内联函数呢？

* 函数体比较长；
* 包含递归调用；
* 包含动态派发（动态绑定）；

##### @inline

```
//永远不会被内联（即使开启了编译器优化）
@inline(never) func test() {
  print("test")
}
```

```
// 开启编译器优化后，即使代码很长，也会被内联（递归调用函数、动态派发的函数除外）
@inline(__always) func test() {
  print("test")
}
```

#### 函数类型（Function Type）

每一个函数都是有类型的，函数类型由**形式参数类型、返回值类型**组成。

##### 函数类型定义变量

```
func test() {}
//函数类型是：() -> Void 或者 () -> ()

func sum(a: Int, b: Int) -> Int {
  a + b
}
// 函数类型是：(Int, Int) -> Int

//定义 (Int, Int) -> Int 函数类型的变量 fn
var fn: (Int, Int) -> Int = sum
fn(2, 3) //结果等于 5
// 通过变量来调用函数时不需要写参数标签
```

##### 函数类型作为函数参数

```
func sum(v1: Int, v2: Int) -> Int {
  v1 + v2
}
func difference(v1: Int, v2: Int) -> Int {
  v1 - v2
}
func pritResult(_ mathFn: (Int, Int) -> Int, _ a: Int, _ b: Int) {
  print("Result:\(mathfn(a, b))")
}
printResult(sum, 5, 2) //Result: 7
printResult(difference, 5, 2) //Result:3
```

##### 函数类型作为函数返回值

返回值是函数类型的函数，叫做**高阶函数**（Higher-Order Function）。如下所示：

```
func next(_ input: Int) -> Int {
  input + 1
}
func previous(_ input: Int) -> Int {
  input - 1
}
func forward(_ forward: Bool) -> (Int) -> Int {
  forward ? next : previous
}
forward(true)(3) //4
forward(false)(3) //2
```

#### typealias

typealias 用来给类型起别名。

```
typealias Byte = Int8
typealias Short = Int16
typealias Long = Int64

//给元组起别名
typealias Date = (year: Int, month: Int, day: Int)
func test(_ date: Date) {
  print(date.0)
  print(date.year)
}
test((2020, 5, 24))

// 给函数类型起别名
typealias IntFn = (Int, Int) -> Int

func difference(v1: Int, v2: Int) -> Int {
  v1 - v2
}
let fn: IntFn = difference
fn(20, 10) //10

func setFn(_ fn: IntFn) { }
setFn(difference)

func getFn() -> IntFn { difference }
```

按照 Swift 标准库的定义，Void 就是空元组。

```
public typealias Void = ()
```

示例：

```
//函数没有返回值
//方式1：不写返回值
func test() {}

//方式2：返回值写 Void
func test() -> Void {}

//方式3：返回值写 空元组
func test() -> () {}
```

#### 嵌套函数（Nested Function）

将函数定义在函数内部叫做“嵌套函数”

```
func forward(_ forward: Bool) -> (Int) -> Int {
  func next(_ input: Int) -> Int {
    input + 1
  }
  func previous(_ input: Int) -> Int {
    input - 1
  }
  return forward ? next : previous
}
forward(true)(3) //4
forward(false)(3) //2
```

## 枚举

### 枚举的基本用法

```
enum Direction {
  case north
  case south
  case east
  case west
}

//等价于
enum Direction {
  case north, south, east, west
}

var dir = Direction.west
dir = Direction.east
dir = .north
print(dir) //north
```

### 关联值（Associated Values）

有时会将枚举的成员值跟其他类型的值关联存储在一起，会非常有用。

```
enum Score {
  case points(Int)
  case grade(Character)
}
var score = Score.points(96)
score = .grade("A")

switch score {
  case let .points(i):
    print(i, "points")
  case let .grade(i):
    print("grade", i)
}
// grade A
```

```
enum Date {
  case digit(year: Int, month: Int, day: Int)
  case string(String)
}
var date = Date.digit(year: 2011, month: 9, day: 10)
date = .string("2020-10-01")

switch date {
case .digit(let year, let month, let day):
  print(year, month, day)
case let .string(value):
  print(value)
}
```

### 原始值（Raw Values）

枚举成员可以使用相同类型的默认值预先关联，这个默认值叫做原始值。

```
enum Grade : String {
  case perfect = "A"
  case great = "B"
  case good = "c"
  case bad = "d"
}
print(Grade.perfect.rawValue) //A
print(Grade.great.rawValue) //B
print(Grade.good.rawValue) //C
print(Grade.bad.rawValue) //D
```

### 隐式原始值

如果枚举的原始值类型是 Int、String，Swift 会自动分配原始值。

```
enum Direction : String {
  case north = "north"
  case south = "south"
  case east = "east"
  case west = "west"
}
//等价于
enum Direction : String {
  case north, south, east, west
}
print(Direction.north) //north
print(Direction.north.rawValue) //north
```

### 递归枚举（Recursive Enumeration） 20202.06.27

```
indirect enum ArithExpr {
  case number(Int)
  case sum(ArithExpr, ArithExpr)
  case difference(ArithExpr, ArithExpr)
}
//等价于
enum ArithExpr {
  case number(Int)
  indirect case sum(ArithExpr, ArithExpr)
  indirect case difference(ArithExpr, ArithExpr)
}
```

递归枚举必须在 enum 前面加上 关键字 indirect，否则会报错。

### MemoryLayout

可以使用 MemoryLayout 获取数据类型占用的内存大小。

```
MemoryLayout<Int>.size //8
MemoryLayout<Int>.stride //8
MemoryLayout<Int>.alignment //8 内存对齐

var age = 10
MemoryLayout.size(ofValue: age) //8
MemoryLayout.stride(ofValue: age) //8 
MemoryLayout.alignment(ofValue: age) //8
```

```
enum Password {
  case number(Int, Int, Int, Int)
  case other
}
MemoryLayout<Password>.size //40字节 分配占用的空间大小
MemoryLayout<Password>.stride //33字节 实际用到的空间大小
MemoryLayout<Password>.alignment //8 对齐参数
```

原始值是不会存储到枚举变量里面的，关联值是存储在枚举变量里的。

### 可选项（Optional） 重点

可选项，一般也叫可选类型，它允许将值设置为 nil。声明方式：在类型名称后面加个问号“?”来定义可选项。

```
var name: String? = "Jack"
name = nil

var age: Int? //默认为nil
age = 10
age = nil
```

```
var array = [1, 15, 40, 29]
fun get(_ index: Int) -> Int? {
  if index < 0 || index >= array.count {
    return nil
  }
  return array[index]
}
print(get(1)) //Optional(15)
print(get(-1)) //nil
print(get(4)) //nil
```

### 强制解包（Forced Unwrapping）

可选项是对其他类型的一层包装，可以将它理解为一个盒子。如果为 nil，那么它是一个空盒子。如果不为 nil，那么盒子里装的是：被包装类型的数据。

**如果要从可选项中取出被包装的数据（将盒子里面装的东西取出来），需要使用感叹号! 进行强制解包**

```
var age: Int? = 10
let ageInt: Int = age!
ageInt += 10
```

**如果对值为 nil 的可选项（空盒子）进行强制解包，将会产生运行时错误**。

```
var age: Int?
age!
//Fatal error: Unexpectedly found nil while unwrapping an Optional value
```

### 判断可选项是否包含值

```
let number = Int("123")
if number != nil {
  print("字符串转换整数成功：\(number!)")
} else {
  print("字符串转换整数失败")
}
//字符串转换整数成功：123
```

### 可选项绑定（Optional Binding）

可以通过**可选项绑定**来判断可选项是否包含值。比如：let number = Int("123")。如果包含就自动解包，把值赋给一个临时的常量（let）或者变量（var），并返回 ture，否则返回 false。

```
if let number = Int("123") {
  print("字符串转换整数成功：\(number)")
  // number 是强制解包之后的 Int 值，所以不用加 !
  // number 作用域仅限于这个大括号
} else {
  print("字符串转换整数失败")
}
//字符串转换整数成功：123
```

```
if let first = Int("4") {
  if let second = Int("42") {
    if first < second && second < 100 {
      print("\(first) < \(second) < 100")
    }
   }
}
// 4 < 42 < 100

//等价于
if let first = Int("4"),
  let second = Int("42"),
  first < second && second < 100 {
  print("\(second) < \(second) < 100")  
}
```

### while 循环中使用可选项绑定

遍历数组，将遇到的正数都加起来，如果遇到负数或者非数字，停止遍历。

```
var strs = ["10", "20", "abc", "-20", "30"]
var index = 0
var sum = 0
//可选项 let num = Int(strs[index]), num > 0
while let num = Int(strs[index]), num > 0 {
  sum += num
  index += 1
}
print(sum)
```

 ### 空合并运算法 ??（Nil-Coalescing Operator）
 
 ```
 a ?? b
 //表示如果 a 不为 nil，则返回a；如果a为nil，则返回b。
 //a 必须是可选项，b可以是可选项，也可以不是可选项。但是 b 和 a 的存储类型必须相同。如果 b 不是可选项，那么返回 a 时会自动解包。
 ```
 
 ```
 let a: Int? = 1
 let b: Int? = 2
 let c = a ?? b
 //c是 Int?，Optional(1)
 ```
 
 ```
 let a: Int? = nil
 let b: Int? = 2
 let c = a ?? b
 //c是 Int?, Optional(2)
 ```
 
 ```
 let a: Int? = 1
 let b: Int = 2
 let c = a ?? b
 //c是 Int, 而不是 Int?，c=1
 ```
 
 ### ?? 跟if let 配合使用
 
 ```
 let a: Int? = nil
 let b: Int? = 2
 if let c = a ?? b {
    print(c)
 }
 //类似于 if a != nil || b != nil
 ```
 
 ```
 let a: Int? = nil
 let b: Int? = 2
 if let c = a, let d = b {
  print(c)
  print(d)
 }
 // 类似于 if a != nil && b != nil
 ```
 
 ### if 语句实现登录 （可选项绑定）
 
 ```
 //字典根据key取出来的值是可选类型。而数组取出来就是真实类型，不是可选类型。
 func login(_ info: [String: String]) {
  let username: String
  //通过可选项绑定来判断可选项是否包含值。比如：let number = Int("123")。如果包含就自动解包，把值赋给一个临时的常量（let）或者变量（var），并返回 ture，否则返回 false。
  if let tmp = info["username"] {
    username = tmp
  } else {
    print("请输入用户名")
    return
  }
  let password: String
  if let tmp = info["password"] {
    password = tmp
  } else {
    print("请输入密码")
    return
  }
  print("用户名：\(username)","密码：\(password)","登录ing")
 }
 login(["username": "jack","password":"123456"])
 login(["password":"123456"])
 login(["username": "jack"])
 ```
 
 ### guard 语句
 
 ```
 guard 条件 else {
  //do something...
  退出当前作用域
  //return、break、continue、throw error
 }
 ```
 
 当 guard 语句的条件为 false时，就会执行大括号里面的代码；当 guard 语句的条件为 true 时，就会跳过 guard 语句。guard 语句特别适合用来“提前退出”。
 
 当使用 guard 语句进行可选项绑定时，绑定的常量（let）、变量（var）也能在外层作用域中使用。
 
 ```
 func login(_ info: [String: String]) {
  guard let username = info["username"] else {
    print("请输入用户名")
    return
  }
  guard let password = info["password"] else {
    print("请输入密码")
    return
  }
  print("用户名：\(username)","密码：\(password)","登录ing")
 }
 ```

### 隐式解包（Implicitly Unwrapped Optional）

在某些情况下，可选项一旦被设定值之后，就会一直拥有值。在这种情况下，可以去掉检查，也不必每次访问的时候都进行解包，因为它能确定每次访问的时候都有值。

隐式解包的定义：在类型后面加一个感叹号!来定义一个隐式解包的可选项。

```
let num1: Int! = 10
let num2: Int = num1
if num1 != nil {
  print(num1 + 6) //16
}
if let num3 = num1 {
  print(num3)
}
```

```
let num1: Int! = nil
let num2: Int = num1 
// Fatal error: Unexpectedly found nil while implicitly unwrapping an Optional value
```

### 字符串插值

可选项在字符串插值或直接打印时，编译器会发出警告。

```
var age: Int? = 10
print("My age is \(age)")
```

消除警告的3种方法：

```
print("My age is \(age!)") //强制解包
print("My age is \(String(describing: age))")
print("My age is \(age ?? 0)")
```

### 多重可选项

```
var num1: Int? = nil
var num2: Int?? = num1 //内层的盒子是 nil
var num3: Int?? = nil //最外层大盒子就是 nil
print(num2 == num3) //false
print(num1 == num3) //false
(num2 ?? 1) ?? 2 //结果为2
(num3 ?? 1) ?? 2 //1
```

可以使用lldb指令**frame variable -R**或 **fr v -R**来查看变量的真实类型。


### 枚举变量内存分布

**原始值**：枚举变量可以使用相同类型的默认值预先对应，这个值叫“原始值”。**原始值不占用枚举变量的内存**。

**关联值**：有时候将枚举的成员值和其他类型的值存储在一起，这种类型的值就是关联值。

**枚举变量内存分布**：枚举中有多个 case 的情况下，1个字节用来存储成员值，N 个字节存储关联值（N 取占用内存最大的关联值）。任何一个 case 的关联值都共用这 N 个字节。

```
enum TestEnum {
  case test
}
print(MemoryLayout<TestEnum>.size) // 0 字节 只有一个枚举值，不需要存储某个值来区分是哪个枚举。
print(MemoryLayout<TestEnum>.stride) // 1 字节
print(MemoryLayout<TestEnum>.alignment) // 1 字节 内存对齐
```

```
enum TestEnum {
  case test(Int)
}
var t = TestEnum.test(10)
print(MemoryLayout<TestEnum>.size) // 8 字节
print(MemoryLayout<TestEnum>.stride) // 8 字节
print(MemoryLayout<TestEnum>.alignment) // 8 字节 内存对齐
```

再增加一个 case 的情况

```
enum TestEnum {
  case test(Int)
  case test1
}
var t = TestEnum.test(10)
print(MemoryLayout<TestEnum>.size) // 9 字节：1个字节用来存储成员值，8个字节用来存储关联值
print(MemoryLayout<TestEnum>.stride) // 16 字节
print(MemoryLayout<TestEnum>.alignment) // 8 字节 内存对齐
```

### 汇编语言（Assembly Language）

**汇编语言**：用符号代替了机器语言的0和1，比机器语言便于阅读和记忆。

```
movw %bx %ax
操作：将寄存器BX的内容送入寄存器AX
```

![编程语言的发展](https://upload-images.jianshu.io/upload_images/4164292-5a4761b91b7483b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 汇编语言的种类

* 8086汇编（16bit）
* x86汇编（32bit）
* x64汇编（64bit）
* ARM汇编（嵌入式、移动设备）

x86、x64汇编根据编译器的不同，有2种书写格式：Intel（Windows派系）和AT&T（Unix派系）。iOS模拟器是AT&T汇编，iOS真机设备是ARM汇编。



#### 常见的汇编指令

![常见的汇编指令](https://upload-images.jianshu.io/upload_images/4164292-59d493f401d94540.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





 

