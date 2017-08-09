---
layout: post
title:  Swift Extensions
categories: swift
keywords: swift
---

扩展为已有的类、结构体、枚举和协议增加新的功能。Swift 中的扩展没有名字。  
扩展可以：  

- 增加 **computed** 实例属性和类型属性 
- 定义实例方法和类型方法
- 提供新的初始化方法  
- 定义下标 
- 定义和使用嵌套类型  
- 使得已有的类型遵守某个协议 

**不可以重写已有的功能**  
****
## 扩展的语法  
    extension SomeType {
        // new functionality to add to SomeType goes here
    }
    extension SomeType: SomeProtocol, AnotherProtocol {
        // implementation of protocol requirements goes here
    }

如果为一个类型定义了扩展，那么这个类型的所有实例都可以使用新增的功能。  

## 扩展 Computed Properties
在扩展中，不可以新增 stored properties，也不能新增 property observers。

    extension Double {
        var km: Double { return self * 1_000.0 }
        var m: Double { return self }
        var cm: Double { return self / 100.0 }
        var mm: Double { return self / 1_000.0 }
        var ft: Double { return self / 3.28084 }
    }
    let oneInch = 25.4.mm
    print("One inch is \(oneInch) meters")
    // Prints "One inch is 0.0254 meters"
    let threeFeet = 3.ft
    print("Three feet is \(threeFeet) meters")
    // Prints "Three feet is 0.914399970739201 meters"

    let aMarathon = 42.km + 195.m
    print("A marathon is \(aMarathon) meters long")  
    // Prints "A marathon is 42195.0 meters long"

## 扩展构造器
只可以新增遍历构造器，不可以新增指定构造器和析构函数。  

    struct Size {
        var width = 0.0, height = 0.0
    }
    struct Point {
        var x = 0.0, y = 0.0
    }
    struct Rect {
        var origin = Point()
        var size = Size()
    }

    extension Rect {
        init(center: Point, size: Size) {
            let originX = center.x - (size.width / 2)
            let originY = center.y - (size.height / 2)
            // 调用指定构造器
            self.init(origin: Point(x: originX, y: originY), size: size)
        }
    }

## 扩展方法  

    extension Int {
        func repetitions(task: () -> Void) {
            for _ in 0..<self {
                task()
            }
        }
    }
    3.repetitions {
        print("Hello!")
    }
    // Hello!
    // Hello!
    // Hello!
    
    
## Mutating Instance Methods

    //一定要有 extension 关键字
    extension Int {
        mutating func square() {
            self = self * self
        }
    }
    var someInt = 3
    someInt.square()
    // someInt is now 9

## 下标

    extension Int {
        subscript(digitIndex: Int) -> Int {
            var decimalBase = 1
            for _ in 0..<digitIndex {
                decimalBase *= 10
            }
            return (self / decimalBase) % 10
        }
    }
    746381295[0]
    // returns 5
    746381295[1]
    // returns 9
    746381295[2]
    // returns 2
    746381295[8]
    // returns 7
    
## 嵌套类型

    extension Int {
        enum Kind {
            case negative, zero, positive
        }
        var kind: Kind {
            switch self {
            case 0:
                return .zero
            case let x where x > 0:
                return .positive
            default:
                return .negative
            }
        }
    }

    func printIntegerKinds(_ numbers: [Int]) {
        for number in numbers {
            switch number.kind {
            case .negative:
                print("- ", terminator: "")
            case .zero:
                print("0 ", terminator: "")
            case .positive:
                print("+ ", terminator: "")
            }
        }
        print("")
    }
    printIntegerKinds([3, 19, -27, 0, -6, 0, 7])
    // Prints "+ + - 0 - 0 + "

## 参考 
- [Extensions](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Extensions.html#//apple_ref/doc/uid/TP40014097-CH24-ID151)

