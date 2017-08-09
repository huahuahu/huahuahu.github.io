---
layout: post
title:  Swift Error Handling
categories: swift
keywords: swift
---

## 表示和抛出错误  
在 Swift 中，错误用遵守 `Error` 协议的值来表示。这个空的协议表示这个类型可以用于错误处理。  
枚举量适合表示错误类型。  

    enum VendingMachineError: Error {
        case invalidSelection
        case insufficientFunds(coinsNeeded: Int)
        case outOfStock
    }

    throw VendingMachineError.insufficientFunds(coinsNeeded: 5) 
    
## 处理错误  
Swift 错误处理方式有四种，会在下面一一介绍。  
和其他语言的异常处理不同，Swift 的错误处理不涉及解除调用栈 (unwinding the call stack)，这是一个代价高昂的操作。因此 `throw` 的性能和 `return` 是可以比拟的。  
### 用 throw 函数传递错误  
用 `throws` 关键字修饰，这种函数叫做 *throwing* 函数。只有 *throwing* 函数可以传递错误。

    func canThrowErrors() throws -> String 
    func cannotThrowErrors() -> String
    
### 用 Do-Catch 处理错误 
    do {
        try expression
        statements
    } catch pattern 1 {
        statements
    } catch pattern 2 where condition {
        statements
    }

### 把错误转化为可选值  
通过 `try?` 把错误转化为可选值。如果发送错误，那么结果是 `nil`。  

    func someThrowingFunction() throws -> Int {
        // ...
    }
     
    let x = try? someThrowingFunction()
     
    let y: Int?
    do {
        y = try someThrowingFunction()
    } catch {
        y = nil
    }
    
用 `try?` 可以使得错误代码变得简洁一些

    func fetchData() -> Data? {
        if let data = try? fetchDataFromDisk() { return data }
        if let data = try? fetchDataFromServer() { return data }
        return nil
    }
    
### 禁用错误传递  
用 `try! ` 修饰，如果发生错误，会产生运行时错误。  

    let photo = try! loadImage(atPath: "./Resources/John Appleseed.jpg")
## 指定清理操作  
用 `defer` 来指定一些操作，在离开代码块之前执行。  
无论是发生什么异常，还是正常执行，这些代码都会执行。  

    func processFile(filename: String) throws {
        if exists(filename) {
            let file = open(filename)
            defer {
                close(file)
            }
            while let line = try file.readline() {
                // Work with the file.
            }
            // close(file) is called here, at the end of the scope.
        }
    }
    
## 参考    
- [Error Handing](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html#//apple_ref/doc/uid/TP40014097-CH42-ID508)
- [code sample](https://github.com/huahuahu/learn/tree/master/swift/Error%20Handling)

