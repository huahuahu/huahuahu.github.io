---
layout: post
title:  Swift Closures
categories: swift
keywords: swift
---

和 C 、OC 中的 blocks 以及其他编程语言的 lambdas 类似。  
全局函数和嵌套函数，是闭包的特殊形式。
## 闭包表达式
以排序函数为例子。`sorted(by:)` 函数接受一个闭包，这个闭包接收两个相同类型的入餐，返回一个布尔值。  

    let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
    func backward(_ s1: String, _ s2: String) -> Bool {
        return s1 > s2
    }
    var reversedNames = names.sorted(by: backward)
    // reversedNames is equal to ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
    

### 闭包表达式语法 
    { (parameters) -> return type in
        statements
    }

其中参数可以是 inout 类型，但是不能有默认值。用闭包的形式重新实现排序方法如下：  
    
    reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
        return s1 > s2
    })
    reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in return s1 > s2 } )

闭包的 body 从 `in` 这个关键字开始。
### 从上下文推断类型  
当闭包作为参数传递时，总是可以推断出参数和返回值的类型，这样子 `->` 也不需要了。

    reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )
    
### 只有一个表达式的闭包，可以省略 `return` 语句    
可以推断出这个闭包的返回值一定是布尔类型，而这个闭包只有一个表达式，所以可以省略。

    reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )

### 参数名速记 (Shorthand Argument Names)    
Swift 用 `$0`、`$1` 等表示不同的参数。可以忽略闭包的参数列表。

    reversedNames = names.sorted(by: { $0 > $1 } )
    
### Operator Methods  
Swift 定义为字符串定义了 `>` 方法。

    reversedNames = names.sorted(by: >)

### Trailing Closures  
如果闭包是一个函数的最后一个参数，而且这个闭包的表达式很长，可以采用 trailing closure 的方法。

    func someFunctionThatTakesAClosure(closure: () -> Void) {
        // function body goes here
    }
     
    // Here's how you call this function without using a trailing closure:
     
    someFunctionThatTakesAClosure(closure: {
        // closure's body goes here
    })
     
    // Here's how you call this function with a trailing closure instead:
     
    someFunctionThatTakesAClosure() {
        // trailing closure's body goes here
    }
    reversedNames = names.sorted() { $0 > $1 }  
    
如果调用闭包的函数只有一个参数，那么可以把 `()`也省略。  

        reversedNames = names.sorted { $0 > $1 }

## Capturing Values
闭包可以对上下文环境中的变量以指针形式保持引用，这样子闭包可以获取或改变这些变量的值。

    func makeIncrementer(forIncrement amount: Int) -> () -> Int {
        var runningTotal = 0
        func incrementer() -> Int {
            runningTotal += amount
            return runningTotal
        }
        return incrementer
    }

`incrementer() ` 函数（闭包）没有定义`runningTotal` ，却可以在函数体内使用这个变量。因为闭包持有了对 `runningTotal` 的引用。这样子在 `makeIncrementer` 函数调用结束之后，仍可以引用这个变量。此外，下次调用 `incrementer()` 函数时，对 `runningTotal` 的改变仍然生效。  

    let incrementByTen = makeIncrementer(forIncrement: 10)
    incrementByTen()
    // returns a value of 10
    incrementByTen()
    // returns a value of 20
    incrementByTen()
    // returns a value of 30

在每次生成一个闭包时，会对周围变量生成一个新的引用，和之前的闭包没有关系。  

    let incrementBySeven = makeIncrementer(forIncrement: 7)
    incrementBySeven()
    // returns a value of 7
    incrementByTen()
    // returns a value of 40
    
    
## 闭包是引用类型
闭包 `incrementBySeven` 虽然是常量，但是闭包中引用的变量仍然可以发生变化。这是因为闭包是引用类型 (reference types)。  
在上面的例子中，`incrementByTen` 引用的闭包是常量，而闭包的内容不是常量。
如果把一个闭包赋值给两个变量，那么这两个变量会指向同一个闭包。

    let alsoIncrementByTen = incrementByTen
    alsoIncrementByTen()
    // returns a value of 50

## Escaping Closures
当闭包作为函数的参数，但却在函数执行完毕之后才执行，称之为这个闭包 “escape a function”。在函数的定义中加上 `@escaping` 关键字。  
一个比较常见的例子是把闭包存储在函数之外的另一个变量中，如下：  

    var completionHandlers: [() -> Void] = []
    func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
        completionHandlers.append(completionHandler)
    }

如果把 `@escaping` 去掉，会报错如下： 
> Passing non-escaping parameter 'completionHandler' to function expecting an @escaping closure

在闭包前声明 `@escaping`，说明在闭包中必须显式引用 `self` 变量。

    class SomeClass {
        var x = 10
        func doSomething() {
            someFunctionWithEscapingClosure { self.x = 100 }
            someFunctionWithNonescapingClosure { x = 200 }
        }
    }
    let instance = SomeClass()
    instance.doSomething()
    print(instance.x)
    // Prints "200"
     
    completionHandlers.first?()
    print(instance.x)
    // Prints "100"
    
##  Autoclosures  
当一个表达式作为参数传递给一个函数时，可能会自动创建一个闭包，闭包的内容就是这个表达式，这种闭包叫做自动闭包 (autoclosure)。  
这种句法上的方便性可以去掉用大括号把代码包裹起来的步骤。  
`assert(condition:message:file:line:) ` 函数的 `condition`参数和 `message` 参数就是自动闭包。  
自动闭包可以延迟计算，其中的代码在实际调用之前，是不会运行的。  

    var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
    print(customersInLine.count)
    // Prints "5"
     
    let customerProvider = { customersInLine.remove(at: 0) }
    print(customersInLine.count)
    // Prints "5"
     
    print("Now serving \(customerProvider())!")
    // Prints "Now serving Chris!"
    print(customersInLine.count)
    // Prints "4"

在上面的代码中，`customersInLine` 类型是 `() -> String`。  
可以声明一个函数，参数类型是 `() -> String`。  

    // customersInLine is ["Alex", "Ewa", "Barry", "Daniella"]
    func serve(customer customerProvider: () -> String) {
        print("Now serving \(customerProvider())!")
    }
    serve(customer: { customersInLine.remove(at: 0) } )
    // Prints "Now serving Alex!"

`serve(customer:)` 这个函数调用时出现了显式的闭包。也可以声明函数时，指定是自动闭包 ` @autoclosure`。这样子在调用时，看起来参数是 `String`。入参被自动转化为闭包。  

    // customersInLine is ["Ewa", "Barry", "Daniella"]
    func serve(customer customerProvider: @autoclosure () -> String) {
        print("Now serving \(customerProvider())!")
    }
    serve(customer: customersInLine.remove(at: 0))
    // Prints "Now serving Ewa!"
    
自动闭包有时会使得函数难以理解。  
autoclosure 和escape closure 可以混用。

    // customersInLine is ["Barry", "Daniella"]
    var customerProviders: [() -> String] = []
    func collectCustomerProviders(_ customerProvider: @autoclosure @escaping () -> String) {
        customerProviders.append(customerProvider)
    }
    collectCustomerProviders(customersInLine.remove(at: 0))
    collectCustomerProviders(customersInLine.remove(at: 0))
     
    print("Collected \(customerProviders.count) closures.")
    // Prints "Collected 2 closures."
    for customerProvider in customerProviders {
        print("Now serving \(customerProvider())!")
    }
    // Prints "Now serving Barry!"
    // Prints "Now serving Daniella!"
    

## 参考  
- [Closures](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Closures.html#//apple_ref/doc/uid/TP40014097-CH11-ID94)

