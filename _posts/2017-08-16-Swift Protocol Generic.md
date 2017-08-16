---
layout: post
title:  Swift Generics
categories: swift
keywords: swift
---

## 泛型函数
## 类型参数  
类型参数放在函数名后面，用尖括号括起来。可以声明多个类型。

    func swapTwoValues<T>(_ a: inout T, _ b: inout T)

## 泛型类型  
除了泛型函数，还可以定义泛型类型。   

    struct Stack<Element> {
        var items = [Element]()
        mutating func push(_ item: Element) {
            items.append(item)
        }
        mutating func pop() -> Element {
            return items.removeLast()
        }
    }
    
### 扩展泛型类型  
在实现时，原定义中什么的泛型，自动可以使用。

    extension Stack {
        var topItem: Element? {
            return items.isEmpty ? nil : items[items.count - 1]
        }
    }
    
## 类型限制  
现在泛型是某个类的子类，或遵守某些协议等等。  

    func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
        // function body goes here
    }

    func findIndex<T: Equatable>(of valueToFind: T, in array:[T]) -> Int? {
        for (index, value) in array.enumerated() {
            if value == valueToFind {
                return index
            }
        }
        return nil
    }

## 关联类型  
在协议中，定义要给关联类型，不指定具体类型，在实现时，才知道。  

    protocol Container {
        associatedtype Item
        mutating func append(_ item: Item)
        var count: Int { get }
        subscript(i: Int) -> Item { get }
    }


    struct IntStack: Container {
        // original IntStack implementation
        var items = [Int]()
        mutating func push(_ item: Int) {
            items.append(item)
        }
        mutating func pop() -> Int {
            return items.removeLast()
        }
        // conformance to the Container protocol
        typealias Item = Int
        mutating func append(_ item: Int) {
            self.push(item)
        }
        var count: Int {
            return items.count
        }
        subscript(i: Int) -> Int {
            return items[i]
        }
    }


也可以用泛型实现  

    struct Stack<Element>: Container {

### 关联类型的限制条件  

    protocol Container {
        associatedtype Item: Equatable
        mutating func append(_ item: Item)
        var count: Int { get }
        subscript(i: Int) -> Item { get }
    }
    
    
    
## 泛型的 where 语句  
在花括号开始之前。

    func allItemsMatch<C1: Container, C2: Container>
        (_ someContainer: C1, _ anotherContainer: C2) -> Bool
        where C1.Item == C2.Item, C1.Item: Equatable {
    

## 带有 where 泛型的扩展 

    extension Stack where Element: Equatable {
        func isTop(_ item: Element) -> Bool {
            guard let topItem = items.last else {
                return false
            }
            return topItem == item
        }
    }
    

## 带有 where 泛型的关联类型  

    protocol Container {
        associatedtype Item
        mutating func append(_ item: Item)
        var count: Int { get }
        subscript(i: Int) -> Item { get }
        
        associatedtype Iterator: IteratorProtocol where Iterator.Element == Item
        func makeIterator() -> Iterator
    }
    

## 泛型下标

    extension Container {
        subscript<Indices: Sequence>(indices: Indices) -> [Item]
            where Indices.Iterator.Element == Int {
                var result = [Item]()
                for index in indices {
                    result.append(self[index])
                }
                return result
        }
    }
    
    
## 参考  
- [Generics](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Generics.html#//apple_ref/doc/uid/TP40014097-CH26-ID553)

