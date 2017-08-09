---
layout: post
title:  Swift Optional Chaining
categories: swift
keywords: swift
---

可选链是通过可选值访问和调用属性、方法、下标的过程。  
如果可选值有值，那么调用会成功；反之，如果可选值是 `nil`，那么调用返回 `nil`。多个调用可以连接在一起形成一个调用链。如果链上任意一环返回 `nil`，那么整个调用链会失败。  
## 可选链作为 Forced Unwrapping 的替代    
通过在可选值后面放一个 `?`，可以定义一个可选链。这个过程和在可选值后面放 `!` 来 force unwrap 一个值类似。不同点是可选链会调用失败，而 force unwrap 会产生运行时错误。  
为了反应可选链可以在 `nil` 上调用，可选链调用的返回值是一个可选值。即使最后的调用函数的返回在不是可选值。  
比如一个属性的类型是 `Int`，如果通过可选链来访问，那么会返回 `Int?`。  

    class Person {
        var residence: Residence?
    }
    
    class Residence {
        var numberOfRooms = 1
    }
    
    let john = Person()
    
    let roomCount = john.residence!.numberOfRooms
    // this triggers a runtime error
    
    //roomCount 的类型是 Int?
    if let roomCount = john.residence?.numberOfRooms {
        print("John's residence has \(roomCount) room(s).")
    } else {
        print("Unable to retrieve the number of rooms.")
    }
    // Prints "Unable to retrieve the number of rooms."


## 通过可选链访问属性  
    let john = Person()
    if let roomCount = john.residence?.numberOfRooms {
        print("John's residence has \(roomCount) room(s).")
    } else {
        print("Unable to retrieve the number of rooms.")
    }
    // Prints "Unable to retrieve the number of rooms."

    // 函数没有被调用，因为左边直接失败了
    john.residence?.address = createAddress()


## 通过可选链调用方法  
如果一个方法没有返回值，那么返回值类型是 `Void`，也就是 `()`，空的元组。  
如果通过可选链调用，返回值是 `Void?`，和 `nil` 比较，可以判定是否调用成功。  

    if john.residence?.printNumberOfRooms() != nil {
        print("It was possible to print the number of rooms.")
    } else {
        print("It was not possible to print the number of rooms.")
    }

通过可选链设置一个实例的属性，返回值也总是 `Void?`。  
> Any attempt to set a property through optional chaining returns a value of type `Void?`  

    if (john.residence?.address = someAddress) != nil {
    print("It was possible to set the address.")
    } else {
        print("It was not possible to set the address.")
    }
    // Prints "It was not possible to set the address."

## 通过可选链访问下标  
    if let firstRoomName = john.residence?[0].name {
        print("The first room name is \(firstRoomName).")
    } else {
        print("Unable to retrieve the first room name.")
    }
    // Prints "Unable to retrieve the first room name."
    
    var testScores = ["Dave": [86, 82, 84], "Bev": [79, 94, 81]]
    testScores["Dave"]?[0] = 91
    testScores["Bev"]?[0] += 1
    testScores["Brian"]?[0] = 72
    // the "Dave" array is now [91, 82, 84] and the "Bev" array is now [80, 94, 81]
    
## 连接多层可用链调用 
    if let johnsStreet = john.residence?.address?.street {
        print("John's street name is \(johnsStreet).")
    } else {
        print("Unable to retrieve the address.")
    }
    // Prints "Unable to retrieve the address."

## 参考 
- [Optional Chaining](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/OptionalChaining.html#//apple_ref/doc/uid/TP40014097-CH21-ID245)
- [code sample](https://github.com/huahuahu/learn/tree/master/swift/OptionalChaining)

