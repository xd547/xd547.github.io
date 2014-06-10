---
layout: post
title: "Swift学习笔记-2-函数和闭包"
date: 2014-06-10 21:10
comments: true
categories: [swift]
---
使用`func`定义函数，通过函数名和参数列表来调用函数，
使用`->`分割参数列表和返回类型
```
func greet(name: String, day: String) -> String {
    return "Hello \(name), today is \(day)"
}
greet("Bob", "Tuesday")
```
使用元组（tuple）返回多个值
```
func getGasPrices -> (Double, Double, Double) {
    return (3.59, 3.69, 3.79)
}
// 还不知道如何获得返回值，会补上
getGasPrices()
```
函数的变长参数会保存在指定名字的数组中
```
func sumOf(numbers:Int...) -> Int {
    var sum = 0
    for number in numbers {
        sum += number
    }

    return sum
}
sumOf()            // 0
sumOf(42, 597, 12) // 651
```
可以使用嵌套函数，嵌套的函数可以访问它所嵌入函数中所的声明的变量。
函数太长或是太复杂的情况下可以使用嵌套函数来组织代码。
```
func returnFifteen() -> Int {
    var y = 0
    func add() {
        y += 5
    }
    add()

    return y
}
returnFifteen() // 5
```
函数是一等类型，这意味着可以把函数当做另一个函数的返回值
```
// 返回类型的解释是：返回 一个接受整形参数返回整形的函数
func makeIncrementer() -> (Int -> Int) {
    func addOne(number:Int) ->Int {
        return 1 + number
    }

    return addOne
}
var increment = makeIncrementer()
increment(7) // 8
```
也意味着函数可以作为另一个函数的参数
```
func hasAnyMathces(list:Int[], condition:Int -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}

func lessThanTen(number:Int) -> Bool {
    return number < 10
}
var numbers = [20, 19, 7, 12]
hasAnyMathces(numbers, lessThanTen) // true
```
函数是闭包的特例，闭包没有名字，使用花括号（`{}`）包围，
使用`in`来分割参数列表返回类型与主体
```
...
numbers.map({
    (number:Int) -> Int in
    let result = 3 * number
    return result
    })
// [60, 57, 21, 36]
```
有几个方法可以简化闭包，当闭包的类型是已知时
（如：委托（delegate）的回调（callback）），可以省略参数的类型，
返回类型，返回语句。而返回值隐含的包含在闭包唯一的语句之中
```
// [60, 57, 21, 36]
numbers.map({ number in 3 * number })
```
使用参数的序号代替参数名，即可写出超短的闭包，
闭包可以作为函数的最后一个参数
```
sort([1, 5, 3, 12, 2]) { $0 > $1 } // [12, 5, 3, 2, 1]
```
