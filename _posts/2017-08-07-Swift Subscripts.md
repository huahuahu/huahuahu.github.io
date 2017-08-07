---
layout: post
title:  Swift Subscripts
categories: swift
keywords: swift
---


类、结构体、枚举都可以定义下标。下标是访问 collection、list、sequence 的快捷方式。  
可以为一个类型定义多个下标。下标也不一定是一维的，可以是多维。  
## 下标语法
语法和实例方法以及 computed property 类似。下标可以是读写或只读的。

    subscript(index: Int) -> Int {
        get {
            // return an appropriate subscript value here
        }
        set(newValue) {
            // perform a suitable setting action here
        }
    }

只读的下标的语法如下，去掉了 `get` 关键字：  

    subscript(index: Int) -> Int {
        // return an appropriate subscript value here
    }
    struct TimesTable {
        let multiplier: Int
        subscript(index: Int) -> Int {
            return multiplier * index
        }
    }
    let threeTimesTable = TimesTable(multiplier: 3)
    print("six times three is \(threeTimesTable[6])")
    // Prints "six times three is 18"
    
## 下标应用  
下标的主要用途是作为快捷方式。

## 下标选项 (Subscript Options)  
下标可以有任意多的参数，参数可以是任意类型。  
**下标也可以使用 variadic 参数，但是不能使用 in-out 参数或为参数指定默认值。**  
类或者结构体可以提供多个下标的实现，叫做**下标重载 (*subscript overloading*)**。  
下标实现可以有多个参数，下面是两个参数的例子。  

    struct Matrix {
        let rows: Int, columns: Int
        var grid: [Double]
        init(rows: Int, columns: Int) {
            self.rows = rows
            self.columns = columns
            grid = Array(repeating: 0.0, count: rows * columns)
        }
        func indexIsValid(row: Int, column: Int) -> Bool {
            return row >= 0 && row < rows && column >= 0 && column < columns
        }
        subscript(row: Int, column: Int) -> Double {
            get {
                assert(indexIsValid(row: row, column: column), "Index out of range")
                return grid[(row * columns) + column]
            }
            set {
                assert(indexIsValid(row: row, column: column), "Index out of range")
                grid[(row * columns) + column] = newValue
            }
        }
    }