---
layout: post
title:  Swift Control Flow
categories: swift
keywords: swift
---

## for-in
1. 如果不需要，可以用 `_` 代替变量名字，这样子省去了起名字的烦恼。

    let base = 3
    let power = 10
    var answer = 1
    for _ in 1...power {
        answer *= base
    }
    print("\(base) to the power of \(power) is \(answer)")
    // Prints "3 to the power of 10 is 59049"

1. `stride` 函数生成一个序列 
    
        let minuteInterval = 5
        for tickMark in stride(from: 0, to: minutes, by: minuteInterval) {
            // render the tick mark every 5 minutes (0, 5, 10, 15 ... 45, 50, 55)
            //不包括最后一个值
        }
        let hours = 12
        let hourInterval = 3
        for tickMark in stride(from: 3, through: hours, by: hourInterval) {
            // render the tick mark every 3 hours (3, 6, 9, 12)
            //可能包括最后一个值
        }

## While 循环  
### while 语句  
    while condition {
        statements
    }

### repeat-while 语句  
和 `do-while` 语句类似
    repeat {
        statements
    } while condition

## 条件语句
### if 语句  
### switch语句
switch 语句对一个值做判断，看它是否符合某种模式。  
switch 语句的条件必须是 exhaustive 的，不能有没有照顾到的分支。  

1. No Implicit Fallthrough  
    在 switch 语句中，一个 case 的代码执行完之后，不需要 `break` 语句。即使没有 `break` 语句，也不会执行到下一个 case 的代码。  
1.  每一个 case 语句必须有相应的代码，否则编译不过  
    
        let anotherCharacter: Character = "a"
        switch anotherCharacter {
        case "a": // Invalid, the case has an empty body
        case "A":
            print("The letter A")
        default:
            print("Not the letter A")
        }
        // This will report a compile-time error.
1. 区间匹配  
    switch 语句的值，可以判断是否在一个区间之内
    
        let approximateCount = 62
        let countedThings = "moons orbiting Saturn"
        let naturalCount: String
        switch approximateCount {
        case 0:
            naturalCount = "no"
        case 1..<5:
            naturalCount = "a few"
        case 5..<12:
            naturalCount = "several"
        case 12..<100:
            naturalCount = "dozens of"
        case 100..<1000:
            naturalCount = "hundreds of"
        default:
            naturalCount = "many"
        }
        print("There are \(naturalCount) \(countedThings).")
        // Prints "There are dozens of moons orbiting Saturn."

1. 元组  
    可以用元组来测试多个值是否满足某个条件。如果对某个值不关心，用 `_` 来表示任何情况。Swift 中允许一个值满足多个 case 条件。如下面语句，值 `(0,0)` 满足所有条件。
    
        let somePoint = (1, 1)
        switch somePoint {
        case (0, 0):
            print("\(somePoint) is at the origin")
        case (_, 0):
            print("\(somePoint) is on the x-axis")
        case (0, _):
            print("\(somePoint) is on the y-axis")
        case (-2...2, -2...2):
            print("\(somePoint) is inside the box")
        default:
            print("\(somePoint) is outside of the box")
        }
        // Prints "(1, 1) is inside the box"
        
1. Value Bindings  
    可以把匹配到的值为一个新的变量赋值。新的变量可以在 case 语句中使用。   
    
        let anotherPoint = (2, 0)
        switch anotherPoint {
        case (let x, 0):
            print("on the x-axis with an x value of \(x)")
        case (0, let y):
            print("on the y-axis with a y value of \(y)")
        case let (x, y):
            print("somewhere else at (\(x), \(y))")
        }
        // Prints "on the x-axis with an x value of 2"

1. Where  语句  
 用 where 语句来表达某种模式。  

        let yetAnotherPoint = (1, -1)
        switch yetAnotherPoint {
        case let (x, y) where x == y:
            print("(\(x), \(y)) is on the line x == y")
        case let (x, y) where x == -y:
            print("(\(x), \(y)) is on the line x == -y")
        case let (x, y):
            print("(\(x), \(y)) is just some arbitrary point")
        }
        
        // Prints "(1, -1) is on the line x == -y"
        
1. Compound Cases  
如果多个 case 语句对应的代码相同，可以把 case 语句合并起来，用逗号分隔。  

        let stillAnotherPoint = (9, 0)
        switch stillAnotherPoint {
        case (let distance, 0), (0, let distance):
            print("On an axis, \(distance) from the origin")
        default:
            print("Not on an axis")
        }
        // Prints "On an axis, 9 from the origin"

## Control Transfer Statements
- continue
- break
- fallthrough
- return
- throw
 
### Fallthrough 
 使得 Swift 的 case 语句和 C 语言类似。  
 
     let integerToDescribe = 5
    var description = "The number \(integerToDescribe) is"
    switch integerToDescribe {
    case 2, 3, 5, 7, 11, 13, 17, 19:
        description += " a prime number, and also"
        fallthrough
    default:
        description += " an integer."
    }
    print(description)
    // Prints "The number 5 is a prime number, and also an integer."
    

## Early Exit
和 if 语句类似，用 `guard` 语句来确保某个条件一定满足。`guard` 语句一定带有一个 `else` 分支，并且在分支中一定要退出 `guard` 语句所在的代码块。

    func greet(person: [String: String]) {
        guard let name = person["name"] else {
            return
        }
        
        print("Hello \(name)!")
        
        guard let location = person["location"] else {
            print("I hope the weather is nice near you.")
            return
        }
        
        print("I hope the weather is nice in \(location).")
    }
     
    greet(person: ["name": "John"])
    // Prints "Hello John!"
    // Prints "I hope the weather is nice near you."
    greet(person: ["name": "Jane", "location": "Cupertino"])
    // Prints "Hello Jane!"
    // Prints "I hope the weather is nice in Cupertino."

## 检测 API 兼容性  
    if #available(iOS 10, macOS 10.12, *) {
        // Use iOS 10 APIs on iOS, and use macOS 10.12 APIs on macOS
    } else {
        // Fall back to earlier iOS and macOS APIs
    }

## 参考  
- [Control Flow](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID120)


