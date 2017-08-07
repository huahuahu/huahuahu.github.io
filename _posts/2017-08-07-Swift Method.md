---
layout: post
title:  Swift Methods
categories: swift
keywords: swift
---

方法是和某一类型关联的函数。**类、结构体、枚举**都可以有实例方法，也可以有类型方法 (type methods)。  
## 实例方法 (Instance Methods)    
实例方法的语法和函数一致。

    class Counter {
        var count = 0
        func increment() {
            count += 1
        }
        func increment(by amount: Int) {
            count += amount
        }
        func reset() {
            count = 0
        }
    }

### `self` 属性  
每一个实例都有一个隐藏的属性叫做 `self`，代表这个实例本身。  

    func increment() {
        self.count += 1
    }
    
实践中，不必经常在代码中写 `self`。在方法的参数和属性名字相同时，需要用 `self` 来指定实例本身的属性。  

    struct Point {
        var x = 0.0, y = 0.0
        func isToTheRightOf(x: Double) -> Bool {
            return self.x > x
        }
    }
    let somePoint = Point(x: 4.0, y: 5.0)
    if somePoint.isToTheRightOf(x: 1.0) {
        print("This point is to the right of the line where x == 1.0")
    }
    // Prints "This point is to the right of the line where x == 1.0"
    
### 使用方法修改值类型  
由于结构体和枚举都是值类型，因此不可以在其实例方法中修改实例。  
可以用 `mutating` 关键字来标识需要修改值类型的方法，包括实例的属性，甚至实例本身。  
Swift 会生成一个新的值，并写回原有的变量中。  

    struct Point {
        var x = 0.0, y = 0.0
        mutating func moveBy(x deltaX: Double, y deltaY: Double) {
            x += deltaX
            y += deltaY
        }
    }
    var somePoint = Point(x: 1.0, y: 1.0)
    somePoint.moveBy(x: 2.0, y: 3.0)
    print("The point is now at (\(somePoint.x), \(somePoint.y))")
    // Prints "The point is now at (3.0, 4.0)"
    
如果这个值类型是常量，那么不能调用一个 mutating 方法。  

    let fixedPoint = Point(x: 3.0, y: 3.0)
    fixedPoint.moveBy(x: 2.0, y: 3.0)
    // this will report an error

### 在 mutating 方法中为 self 赋值  
    struct Point {
        var x = 0.0, y = 0.0
        mutating func moveBy(x deltaX: Double, y deltaY: Double) {
            self = Point(x: x + deltaX, y: y + deltaY)
        }
    }
    
    enum TriStateSwitch {
        case off, low, high
        mutating func next() {
            switch self {
            case .off:
                self = .low
            case .low:
                self = .high
            case .high:
                self = .off
            }
        }
    }
    var ovenLight = TriStateSwitch.low
    ovenLight.next()
    // ovenLight is now equal to .high
    ovenLight.next()
    // ovenLight is now equal to .off
    
## 类型方法 (Type Methods)  
类型方法和某一个类关联，而不是某一个实例。  
用 `static` 来声明类型方法。对于类来说，可以用 `class` 来代替 `static`，这样子这个方法可以被子类 override。  
**类、结构体、枚举都可以有自己的类型方法。**  

    class SomeClass {
        class func someTypeMethod() {
            // type method implementation goes here
        }
    }
    SomeClass.someTypeMethod()

在类型方法中，`self` 指代这个类，而不是某一实例。  

    struct LevelTracker {
        static var highestUnlockedLevel = 1
        var currentLevel = 1
        
        static func unlock(_ level: Int) {
            if level > highestUnlockedLevel { highestUnlockedLevel = level }
        }
        
        static func isUnlocked(_ level: Int) -> Bool {
            return level <= highestUnlockedLevel
        }
        
        @discardableResult
        mutating func advance(to level: Int) -> Bool {
            if LevelTracker.isUnlocked(level) {
                currentLevel = level
                return true
            } else {
                return false
            }
        }
    }
    


