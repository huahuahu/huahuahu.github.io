---
layout: post
title:  Swift Enumerations
categories: swift
keywords: swift
---

枚举在 Swift 中是 first-class types。支持很多和类相似的操作，比如实例方法、初始化函数等。  
## 枚举语法  
    enum CompassPoint {
        case north
        case south
        case east
        case west
    }

和 C 语言不通，每一个枚举常量不会被赋予一个整数值。它们属于新的枚举数据类型。  
也可以把多个枚举常量定义在一行。  

    enum Planet {
        case mercury, venus, earth, mars, jupiter, saturn, uranus, neptune
    }

在赋值时，如果知道类型，可以省去 `.` 前的类型。

    var directionToHead = CompassPoint.west
    directionToHead = .east

## 用 switch 语句来匹配枚举
    directionToHead = .south
    switch directionToHead {
    case .north:
        print("Lots of planets have a north")
    case .south:
        print("Watch out for penguins")
    case .east:
        print("Where the sun rises")
    case .west:
        print("Where the skies are blue")
    }
    // Prints "Watch out for penguins"
    
## Associated Values  
每一个枚举值可以有一个关联值，关联值的类型可以不同。并且可以修改枚举值。  

    enum Barcode {
        case upc(Int, Int, Int, Int)
        case qrCode(String)
    }
    var productBarcode = Barcode.upc(8, 85909, 51226, 3)
    productBarcode = .qrCode("ABCDEFGHIJKLMNOP")
    switch productBarcode {
    case .upc(let numberSystem, let manufacturer, let product, let check):
        print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
    case .qrCode(let productCode):
        print("QR code: \(productCode).")
    }
    //// Prints "QR code: ABCDEFGHIJKLMNOP."
    
    
    productBarcode = Barcode.upc(8, 85909, 51226, 3)
    var br:Barcode = .upc(1, 1, 1, 1)
    print(productBarcode)
    ////upc(8, 85909, 51226, 3)
    print(br)
    //upc(1, 1, 1, 1)
    
## Raw Values
每一个枚举值可以有一个 raw value。每一种枚举类型，所有枚举值的类型都是相同的，且在定义枚举类型时就指定了，不能变化。  
raw value 可以是整数、浮点数、字符串。 
    
    enum ASCIIControlCharacter: Character {
        case tab = "\t"
        case lineFeed = "\n"
        case carriageReturn = "\r"
    }

### 隐式声明 raw value  
1. Int 型，默认递增    
    
        enum Planet: Int {
            case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
        }
2. String 型，默认和枚举值相同
    
        enum CompassPoint: String {
            case north, south, east, west
        }

3. 通过 `rawValue` 变量访问 rawValue  
    
        let earthsOrder = Planet.earth.rawValue
        // earthsOrder is 3
         
        let sunsetDirection = CompassPoint.west.rawValue
        // sunsetDirection is "west"
    
    
### 从 raw value 初始化枚举量  
    let possiblePlanet = Planet(rawValue: 7)
    // possiblePlanet is of type Planet? and equals Planet.uranus


## 递归枚举
指这个枚举的 associated value 的类型也是这个枚举类型。

    indirect enum ArithmeticExpression {
        case number(Int)
        case addition(ArithmeticExpression, ArithmeticExpression)
        case multiplication(ArithmeticExpression, ArithmeticExpression)
    }

    let five = ArithmeticExpression.number(5)
    let four = ArithmeticExpression.number(4)
    let sum = ArithmeticExpression.addition(five, four)
    let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))
    
    func evaluate(_ expression: ArithmeticExpression) -> Int {
        switch expression {
        case let .number(value):
            return value
        case let .addition(left, right):
            return evaluate(left) + evaluate(right)
        case let .multiplication(left, right):
            return evaluate(left) * evaluate(right)
        }
    }
     
    print(evaluate(product))
    // Prints "18"

## 参考  
- [Enumerations](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Enumerations.html#//apple_ref/doc/uid/TP40014097-CH12-ID145)

