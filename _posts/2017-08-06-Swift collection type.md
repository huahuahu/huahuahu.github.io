---
layout: post
title:  Swift Collection Types
categories: swift
keywords: swift
---

Swift 提供了三种 collection type，数组、集合、字典。  
这三种 collection type 对于可以存储什么类型的数据做了校验。因此不会插入错错误类型的值。  
## 数组  
### 数组类型的语法  
Swift 数组完整形式是 `Array<Element>`，简写为 `[Element]`。一般用简写形式。  
### 创建空数组  
1. 用初始化方法  
    
        var someInts = [Int]()
        print("someInts is of type [Int] with \(someInts.count) items.")
        // Prints "someInts is of type [Int] with 0 items."
2. 如果数组类型可以推断出来，可以用 `[]` 来创建 

        someInts.append(3)
        // someInts now contains 1 value of type Int
        someInts = []
        // someInts is now an empty array, but is still of type [Int]

### 用默认值创建数组  

    var threeDoubles = Array(repeating: 0.0, count: 3)
    // threeDoubles is of type [Double], and equals [0.0, 0.0, 0.0]

### 用 `+` 把两个数组连接为一个新数组  

    var anotherThreeDoubles = Array(repeating: 2.5, count: 3)
    // anotherThreeDoubles is of type [Double], and equals [2.5, 2.5, 2.5]
     
    var sixDoubles = threeDoubles + anotherThreeDoubles
    // sixDoubles is inferred as [Double], and equals [0.0, 0.0, 0.0, 2.5, 2.5, 2.5]

### 用数组字面量来创建数组  

    var shoppingList: [String] = ["Eggs", "Milk"]
    // shoppingList has been initialized with two initial items
    var shoppingList = ["Eggs", "Milk"]
    
### 修改数组  
1. 用 `append` 函数为数组增加元素  
    
        shoppingList.append("Flour")
        // shoppingList now contains 3 items, and someone is making pancakes
1. 把另一个数组内容加在后面  
    
        shoppingList += ["Baking Powder"]
        // shoppingList now contains 4 items
        shoppingList += ["Chocolate Spread", "Cheese", "Butter"]
        // shoppingList now contains 7 items
1. 用下标来改变某个范围内数组内容  
    
        shoppingList[4...6] = ["Bananas", "Apples"]
        // shoppingList now contains 6 items
1. 插入和删除操作  

        shoppingList.insert("Maple Syrup", at: 0)
        // shoppingList now contains 7 items
        // "Maple Syrup" is now the first item in the list
        
        let mapleSyrup = shoppingList.remove(at: 0)
        // the item that was at index 0 has just been removed
        // shoppingList now contains 6 items, and no Maple Syrup
        // the mapleSyrup constant is now equal to the removed "Maple Syrup" string
        let apples = shoppingList.removeLast()

### 遍历数组  
1. for-in 循环 
    
        for item in shoppingList {
            print(item)
        }
1. 用 `enumerated()` 来获取索引  
    
        for (index, value) in shoppingList.enumerated() {
            print("Item \(index + 1): \(value)")
        }

## 集合  
集合中的元素必须是可哈希的 (hashable)。  
### 语法  
`Set<Element>`，没有简写形式。  
### 初始化空集合  
    
    var letters = Set<Character>()
    print("letters is of type Set<Character> with \(letters.count) items.")
    // Prints "letters is of type Set<Character> with 0 items."
    letters.insert("a")
    // letters now contains 1 value of type Character
    letters = []
    // letters is now an empty set, but is still of type Set<Character>

### 用 array literal 创建集合  
注意这里 `Set` 类型声明不能省略，否则会推断为数组。

    var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]
    // favoriteGenres has been initialized with three initial items
    var favoriteGenres: Set = ["Rock", "Classical", "Hip hop"]

### 修改集合  

    favoriteGenres.insert("Jazz")
    // favoriteGenres now contains 4 items

`remove` 函数，如果成功移除，会返回移除的内容，否则返回 nil。

    if let removedGenre = favoriteGenres.remove("Rock") {
        print("\(removedGenre)? I'm over it.")
    } else {
        print("I never much cared for that.")
    }
    // Prints "Rock? I'm over it."

### 遍历集合  
    for genre in favoriteGenres {
        print("\(genre)")
    }
    for genre in favoriteGenres.sorted() {
        print("\(genre)")
    }

### 集合运算  
![](http://oda58fqub.bkt.clouddn.com/15019980766146.png)
    
    let oddDigits: Set = [1, 3, 5, 7, 9]
    let evenDigits: Set = [0, 2, 4, 6, 8]
    let singleDigitPrimeNumbers: Set = [2, 3, 5, 7]
     
    oddDigits.union(evenDigits).sorted()
    // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    oddDigits.intersection(evenDigits).sorted()
    // []
    oddDigits.subtracting(singleDigitPrimeNumbers).sorted()
    // [1, 9]
    oddDigits.symmetricDifference(singleDigitPrimeNumbers).sorted()
    // [1, 2, 9]

### 集合关系 
    let houseAnimals: Set = ["🐶", "🐱"]
    let farmAnimals: Set = ["🐮", "🐔", "🐑", "🐶", "🐱"]
    let cityAnimals: Set = ["🐦", "🐭"]
     
    houseAnimals.isSubset(of: farmAnimals)
    // true
    farmAnimals.isSuperset(of: houseAnimals)
    // true
    farmAnimals.isDisjoint(with: cityAnimals)
    // true

## 字典
### 语法  
`Dictionary<Key, Value>`，简写为 `[Key: Value]`。其中 `Key` 必须遵守 `Hashable` 协议。  
### 创建和初始化空字典  
    var namesOfIntegers = [Int: String]()
    // namesOfIntegers is an empty [Int: String] dictionary
    namesOfIntegers[16] = "sixteen"
    // namesOfIntegers now contains 1 key-value pair
    namesOfIntegers = [:]
    // namesOfIntegers is once again an empty dictionary of type [Int: String]  
    
### 用 Dictionary Literal 创建字典
    var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
    var airports = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]  
    
### 修改字典
1. 用下标增加元素  
    
        airports["LHR"] = "London"
        // the airports dictionary now contains 3 items
 
2. 用下标修改元素
    
        airports["LHR"] = "London Heathrow"
        // the value for "LHR" has been changed to "London Heathrow"
        
1. 也可以用 `updateValue(_:forKey:) ` 进行增加、修改操作。如果有变化，会返回修改前的值。  
    
        if let oldValue = airports.updateValue("Dublin Airport", forKey: "DUB") {
            print("The old value for DUB was \(oldValue).")
        }
        // Prints "The old value for DUB was Dublin."

1. 删除操作  
 
        airports["APL"] = nil
        // APL has now been removed from the dictionary
        if let removedValue = airports.removeValue(forKey: "DUB") {
            print("The removed airport's name is \(removedValue).")
        } else {
            print("The airports dictionary does not contain a value for DUB.")
        }
        // Prints "The removed airport's name is Dublin Airport."

### 遍历字典  
1. 遍历 key 和value

        for (airportCode, airportName) in airports {
            print("\(airportCode): \(airportName)")
        }
        // YYZ: Toronto Pearson
        // LHR: London Heathrow
    
1. 遍历 key
    
        for airportCode in airports.keys {
            print("Airport code: \(airportCode)")
        }
        // Airport code: YYZ
        // Airport code: LHR

2. 遍历 value
    
        for airportName in airports.values {
            print("Airport name: \(airportName)")
        }
        // Airport name: Toronto Pearson
        // Airport name: London Heathrow

## 参考 
- [Collection Types](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html#//apple_ref/doc/uid/TP40014097-CH8-ID105)


