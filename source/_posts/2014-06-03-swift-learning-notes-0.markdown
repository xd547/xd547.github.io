---
layout: post
title: "Swift学习笔记0"
date: 2014-06-03 09:39
comments: true
categories: [swift]
---
Apple在昨天的WWDC上发布了新的语言Swift，非常值得一学，
主要用来做书[The Swift Programming Language](https://itunes.apple.com/us/book/the-swift-programming-language/id881256329?mt=11)
（iBooks）的笔记和一些自己的想法。

Hello,world不用载入其他的库，不需要main函数，不需要分号结尾
```
println("Hello， world")
```
用`var`声明变量，用`let`声明常量,
编译器根据变量和常量的赋值推断其类型
```
var myVariable = 42
myVariable = 50
let myConstant = 42
```
也可以显式的指定类型
```
let explicitDouble: Double = 70
```
不能隐式改变类型，要显式改变
```
let label = "The width is"
let width = 94
let widthLabel = label + String(width)
```
`\()`内嵌字符串
```
let apples = 3
let oranges= 5
let appleSummary = "I have \(apples) apples."
let fruitSummary = "I have \(apples + oranges) pieces of fruit."
```
下面这点让我开始喜欢Swift，数组（array）和字典（dictionary）
统一使用`[]`创建
```
var shoppingList = ["catfish", "water", "tulips", "blue paint"]
shoppingList[1] = "bottle of water" // 这点可以看出Swift也是从0开始

var occupations = [
    "Malcolm": "Captain",
    "Kaylee": "Mechanic",
]
occupations["Jayne"] = "Public Relations"
```
使用initializer syntax创建空数组和字典
```
let emptyArray = String[]()
let emptyDictionary = Dictionary<String, Float>()
// 说明Swift是强类型的，不像脚本语言可以装任意对象在数组和字典里面
// 字典的key只能使用 String，而不是任何对象
```
如果类型可以确定，使用`[]`创建并重新赋值数组，使用`[:]`创建并重新赋值字典。
```
shoppingList = [] // 已经去购物了，而且东西都买到了，也就是shopping置空
```
