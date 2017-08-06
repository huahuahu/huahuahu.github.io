---
layout: post
title:  Swift Functions
categories: swift
keywords: swift
---

## 定义和调用函数  
    func greet(person: String) -> String {
        let greeting = "Hello, " + person + "!"
        return greeting
    }
    print(greet(person: "Anna"))
    // Prints "Hello, Anna!"
    print(greet(person: "Brian"))
    // Prints "Hello, Brian!"
    
## 函数参数和返回值  
### 没有参数 
    func sayHelloWorld() -> String {
        return "hello, world"
    }
    print(sayHelloWorld())
    // Prints "hello, world"
    
### 多个参数  
    func greet(person: String, alreadyGreeted: Bool) -> String {
        if alreadyGreeted {
            return greetAgain(person: person)
        } else {
            return greet(person: person)
        }
    }
    print(greet(person: "Tim", alreadyGreeted: true))
    // Prints "Hello again, Tim!"

### 没有返回值 
    func greet(person: String) {
        print("Hello, \(person)!")
    }
    greet(person: "Dave")
    // Prints "Hello, Dave!"
    
### 多个返回值  
    func minMax(array: [Int]) -> (min: Int, max: Int) {
        var currentMin = array[0]
        var currentMax = array[0]
        for value in array[1..<array.count] {
            if value < currentMin {
                currentMin = value
            } else if value > currentMax {
                currentMax = value
            }
        }
        return (currentMin, currentMax)
    }
    
    let bounds = minMax(array: [8, -6, 2, 109, 3, 71])
    print("min is \(bounds.min) and max is \(bounds.max)")
    // Prints "min is -6 and max is 109"

### Optional 元组返回值
注意 `(Int, Int)? `和 `(Int?, Int?)` 不同。

    func minMax(array: [Int]) -> (min: Int, max: Int)? {
        if array.isEmpty { return nil }
        var currentMin = array[0]
        var currentMax = array[0]
        for value in array[1..<array.count] {
            if value < currentMin {
                currentMin = value
            } else if value > currentMax {
                currentMax = value
            }
        }
        return (currentMin, currentMax)
    }
    if let bounds = minMax(array: [8, -6, 2, 109, 3, 71]) {
        print("min is \(bounds.min) and max is \(bounds.max)")
    }
    // Prints "min is -6 and max is 109"
    
## Function Argument Labels and Parameter Names
每一个参数有一个 argument label，用于调用时。也有一个 parameter name，用于函数体内。所有参数的 parameter name 必须不同，argument label 可以重复。  

    func greet(person: String, from hometown: String) -> String {
        return "Hello \(person)!  Glad you could visit from \(hometown)."
    }
    print(greet(person: "Bill", from: "Cupertino"))
    // Prints "Hello Bill!  Glad you could visit from Cupertino."
    
### 忽略 argument label  
    func someFunction(_ firstParameterName: Int, secondParameterName: Int) {
        // In the function body, firstParameterName and secondParameterName
        // refer to the argument values for the first and second parameters.
    }
    someFunction(1, secondParameterName: 2)
    
## 默认参数  
    func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
        // If you omit the second argument when calling this function, then
        // the value of parameterWithDefault is 12 inside the function body.
    }
    someFunction(parameterWithoutDefault: 3, parameterWithDefault: 6) // parameterWithDefault is 6
    someFunction(parameterWithoutDefault: 4) // parameterWithDefault is 12

## Variadic Parameters 
即可以接受不定项个值得参数。 一个函数至多有一个 variadic parameter。

    func arithmeticMean(_ numbers: Double...) -> Double {
        var total: Double = 0
        for number in numbers {
            total += number
        }
        return total / Double(numbers.count)
    }
    arithmeticMean(1, 2, 3, 4, 5)
    // returns 3.0, which is the arithmetic mean of these five numbers
    arithmeticMean(3, 8.25, 18.75)
    // returns 10.0, which is the arithmetic mean of these three numbers

## In-Out Parameters
把关键字 `inout` 放在参数前，在函数内改变参数值，会影响到调用者。  
调用时，放 `&` 在入参之前。  
这类参数不能有默认值，也不能是 variadic parameters。  

    func swapTwoInts(_ a: inout Int, _ b: inout Int) {
        let temporaryA = a
        a = b
        b = temporaryA
    }
    var someInt = 3
    var anotherInt = 107
    swapTwoInts(&someInt, &anotherInt)
    print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
    // Prints "someInt is now 107, and anotherInt is now 3"

## 函数类型  
每一个函数都对应一个函数类型，可以用于返回值或参数类型。  
### 函数类型作为参数 
    var mathFunction: (Int, Int) -> Int = addTwoInts
    func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
        print("Result: \(mathFunction(a, b))")
    }
    printMathResult(addTwoInts, 3, 5)
    // Prints "Result: 8"

### 函数类型作为返回值  
    func stepForward(_ input: Int) -> Int {
        return input + 1
    }
    func stepBackward(_ input: Int) -> Int {
        return input - 1
    }
    func chooseStepFunction(backward: Bool) -> (Int) -> Int {
        return backward ? stepBackward : stepForward
    }
    var currentValue = 3
    let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
    // moveNearerToZero now refers to the stepBackward() function

## Nested Functions
在一个函数体内定义一个函数。
嵌套函数对外是不可见的，可以被 enclosing 函数调用。enclosing 函数也可以返回一个嵌套函数，这样子嵌套函数可以在其他地方被调用。

       func chooseStepFunction(backward: Bool) -> (Int) -> Int {
           func stepForward(input: Int) -> Int { return input + 1 }
           func stepBackward(input: Int) -> Int { return input - 1 }
           return backward ? stepBackward : stepForward
       }
       var currentValue = -4
       let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
       // moveNearerToZero now refers to the nested stepForward() function
       while currentValue != 0 {
           print("\(currentValue)... ")
           currentValue = moveNearerToZero(currentValue)
       }
       print("zero!")
       // -4...
       // -3...
       // -2...
       // -1...
       // zero!
        
        
## 参考  
- [Functions](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Functions.html#//apple_ref/doc/uid/TP40014097-CH10-ID158)    
    


