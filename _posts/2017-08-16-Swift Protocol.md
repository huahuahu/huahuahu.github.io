---
layout: post
title:  Swift Protocols
categories: swift
keywords: swift
---

Protocol 可以被类、结构体、枚举采用。  

## Protocol 语法  

    protocol SomeProtocol {
        // protocol definition goes here
    }

类和结构体遵守协议的语法  

    struct SomeStructure: FirstProtocol, AnotherProtocol {
        // structure definition goes here
    }
    class SomeClass: SomeSuperclass, FirstProtocol, AnotherProtocol {
        // class definition goes here
    }
    
    
## Property Requirements  
协议可以要求准守它的类型提供某些特定名字的实例属性或类型属性，并不要求属性是 stored property 或 computed property。  

    protocol SomeProtocol {
        var mustBeSettable: Int { get set }
        var doesNotNeedToBeSettable: Int { get }
    }

类型属性用 `static` 关键字修饰。  

    protocol AnotherProtocol {
        static var someTypeProperty: Int { get set }
    }
    
## Method Requirements
协议可以要求准守它的类型实现特定的实例方法或类型方法。  
Protocol 中声明的方法中，不能有默认值。  

    protocol SomeProtocol {
        static func someTypeMethod()
    }

    protocol RandomNumberGenerator {
        func random() -> Double
    }
    
### Mutating Method Requirements 
方法带有 `mutating` 关键字，实现协议的结构体/枚举可以改变自身。

    protocol Togglable {
        mutating func toggle()
    }

##  Initializer Requirements
    
    protocol SomeProtocol {
        init(someParameter: Int)
    }

### 遵守协议的类需要把构造器用 `required` 修饰
    class SomeClass: SomeProtocol {
        required init(someParameter: Int) {
            // initializer implementation goes here
        }
    }

既重写了父类的构造器，又遵守某个协议，那么需要同时用 `override`、`required` 修饰。  

    protocol SomeProtocol {
        init()
    }
     
    class SomeSuperClass {
        init() {
            // initializer implementation goes here
        }
    }
     
    class SomeSubClass: SomeSuperClass, SomeProtocol {
        // "required" from SomeProtocol conformance; "override" from SomeSuperClass
        required override init() {
            // initializer implementation goes here
        }
    }
    
### Failable Initializer Requirements 
协议可以声明可失败的构造器，可以用可失败构造器或不可失败构造器实现。  
协议声明不可以失败的构造器，可以用不可失败构造器或 implicitly unwrapped 可失败构造器实现。  

## 协议作为类型  

    class Dice {
        let sides: Int
        let generator: RandomNumberGenerator
        init(sides: Int, generator: RandomNumberGenerator) {
            self.sides = sides
            self.generator = generator
        }
        func roll() -> Int {
            return Int(generator.random() * Double(sides)) + 1
        }
    }

## 代理  
代理是一种设计模式，让类或结构体把事情交给另一个类型的实例来实现。

    protocol DiceGame {
        var dice: Dice { get }
        func play()
    }
    protocol DiceGameDelegate {
        func gameDidStart(_ game: DiceGame)
        func game(_ game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int)
        func gameDidEnd(_ game: DiceGame)
    }
    class SnakesAndLadders: DiceGame {
        let dice = Dice(sides: 6, generator: LinearCongruentialGenerator())
        ...
        var delegate: DiceGameDelegate?
        func play() {
            square = 0
            delegate?.gameDidStart(self)
            gameLoop: while square != finalSquare {
                let diceRoll = dice.roll()
                delegate?.game(self, didStartNewTurnWithDiceRoll: diceRoll)
                switch square + diceRoll {
                case finalSquare:
                ...
    }
    

## 用扩展来实现协议  
    protocol TextRepresentable {
        var textualDescription: String { get }
    }
    extension Dice: TextRepresentable {
        var textualDescription: String {
            return "A \(sides)-sided dice"
        }
    }

### 用扩展来声明遵守协议  
如果一个类型已经实现了协议的所有方法和变量，可以通过一个空的扩展来声明这个类型实现了这个协议。  

    struct Hamster {
        var name: String
        var textualDescription: String {
            return "A hamster named \(name)"
        }
    }
    extension Hamster: TextRepresentable {}
    
    
## Collections of Protocol Types
可以把实现某个协议的所有实例放入一个集合类型中。  

    let things: [TextRepresentable] = [game, d12, simonTheHamster]
    for thing in things {
        print(thing.textualDescription)
    }
    
## 协议继承  
协议可以继承**一个或多个**协议，并加入其他的方法或变量。  

    protocol InheritingProtocol: SomeProtocol, AnotherProtocol {
        // protocol definition goes here
    }

### 只适用于类的协议  
只需要继承 `AnyObject` 协议即可。

    protocol SomeClassOnlyProtocol: AnyObject, SomeInheritedProtocol {
        // class-only protocol definition goes here
    }

## Protocol Composition （协议组合）
1. 把若干个协议组合，作为临时的类型。要求类型同时遵守这些协议。  

        protocol Named {
            var name: String { get }
        }
        protocol Aged {
            var age: Int { get }
        }
        func wishHappyBirthday(to celebrator: Named & Aged) {
            print("Happy birthday, \(celebrator.name), you're \(celebrator.age)!")
        }
        

1. 也可把类型和协议组合，要求类型同时是某个类的子类，并且遵守某些协议。  

        class Location {
        }
        class City: Location, Named {
            ...
        }
        func beginConcert(in location: Location & Named) {
            print("Hello, \(location.name)!")
        }
        
    
## 检查是否遵守协议    
和类型一样，可以用 `as` 和 `is` 来判断。  

    protocol HasArea {
        var area: Double { get }
    }
    for object in objects {
        if let objectWithArea = object as? HasArea {
            print("Area is \(objectWithArea.area)")
        } else {
            print("Something that doesn't have an area")
        }
    }
    
## Optional Protocol Requirements  
协议和可选的方法或变量，都必须用 `@objc` 来修饰。  
这些协议只能被从 OC 继承来的类或其他  `@objc` 类来遵守，不能用结构体或枚举实现。  
在调用这些属性或方法是，须用可选链。

    @objc protocol CounterDataSource {
        @objc optional func increment(forCount count: Int) -> Int
        @objc optional var fixedIncrement: Int { get }
    }
    class Counter {
        var count = 0
        var dataSource: CounterDataSource?
        func increment() {
            if let amount = dataSource?.increment?(forCount: count) {
                count += amount
            } else if let amount = dataSource?.fixedIncrement {
                count += amount
            }
        }
    }


## Protocol Extensions （协议扩展  ）
协议可以扩展来实现方法或提供变量，这样子不用在每一个遵守协议的类型中重复实现。下面是一个例子。  

    extension RandomNumberGenerator {
        func randomBool() -> Bool {
            return random() > 0.5
        }
    }


## 为协议扩展增加限制条件  
只有满足这些条件的协议，才可以使用协议扩展中定义的方法和属性。  

    extension Collection where Iterator.Element: TextRepresentable {
        var textualDescription: String {
            let itemsAsText = self.map { $0.textualDescription }
            return "[" + itemsAsText.joined(separator: ", ") + "]"
        }
    }


## 参考  
- [Protocols](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-ID267)

