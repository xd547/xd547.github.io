---
layout: post
title: "Swift学习笔记-0-流程控制 "
date: 2014-06-05 09:14
comments: true
categories: [swift]
---
条件语句使用`if`，`switch`，循环使用`for-in`，`for`，`while`和`do-while`，
条件变量和循环变量的圆括号可选，条件体和循环体必须有花括号。
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
teamScore // 11
```

`if`语句中，条件必须是布尔表达式，也就是说`if socre { ... }`是错误的，
没有隐含的0比较.

一个变量可能没有值的时候，`if`和`let`可以一起使用，这些值通过
可选值（optional value）表示。一个可选值要么包含一个值，要么是`nil`，
在类型后面加问号（?）就可以把一个值标记为可选值
```
var optionalString: String? = "Hello"
optionalString == nil

var optionalName: String? = "John Appleseed"
//var optionalName: String? = nil
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)"
} else {
    greeting = "Hello, world"
}
```

可选值为`nil`条件的结果是`false`，用`let`声明的`name`的作用域
仅在`if-else`结构中

`switch`可以使用任何种类的比较数据，而不只是整型数值和相等的比较
```
let vegetable = "red pepper"
switch vegetable {
case "celery":
    let vegetableComment = "Add some raisins and make ants on a log."
case "cucumber", "watercress":
    let vegetableComment = "That would make a good tea sandwich"
case let x where x.hasSuffix("pepper"):
    let vegetableComment = "Is it a spicy \(x)?"
default:
    let vegetableComment = "Everything tastes good in soup"
}
```
代码执行时如果`switch case`匹配，`switch`就会退出，不会执行后面的`case`，
所以不必要在每个`case`结束之后加入`break`
要注意的是在`switch`必须要有一个`default`，否则会报错

使用键值对在`for-in`遍历字典
```
let interestingNumbers = [
    "Prime": [2, 3, 5, 7, 11, 13],
    "Fibonacci": [1, 1, 2, 3, 5, 8],
    "Square": [1, 4, 9, 16, 25],
]

var largest = 0;
var largestKind = "";
for (kind, numbers) in interestingNumbers {
    for number in numbers {
        if number > largest {
            largest = number
            largestKind = kind
        }
    }
}
largest      // 25
largestKind  // Square
```

用`while`来执行一段代码直到条件改变，条件可以放在循环的最后（`do-while`），
来保证循环至少执行一次
```
var n = 2
while n < 100 {
    n = n * 2
}

n // 128

var m = 2

do {
    m = m * 2
} while m < 100
m // 128
```

在循环中使用range`..`或是初始化，条件，增量的形式来获得索引
```
var firstForLoop = 0
for i in 0..3 {
    firstForLoop += i
}

firstForLoop // 3

var secondForLoop = 0
for var i = 0; i < 3; ++i {
    secondForLoop += 1
}
secondForLoop // 3
```
`..`排除最后一个值
`...`包含最后一个值
