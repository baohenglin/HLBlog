1. ## let 声明常量 var声明变量

```
let myConstant = 42
var myVariable = 42
myVariable = 50
```

编译器会自动推断出 myVariable 是一个整数类型，因为它的初始值是整数。

如果初始值没有提供足够的信息（或者没有初始值），那么就需要在变量后面声明类型，用冒号分割。
