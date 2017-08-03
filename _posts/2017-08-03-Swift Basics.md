---
layout: post  
title:  swift The Basics  
categories: swift  
keywords: swift  
---

1. 一行中声明多个变量  

        var red, green, blue: Double  
1. 注释  
    - `//`之后，注释一行    
    
            // This is a comment.
    -  `/* */`包裹起来，支持多行  
        
            /* This is also a comment
            but is written over multiple lines. */      
    -  支持注释嵌套  
        
            /* This is the start of the first multiline comment.
             /* This is the second, nested multiline comment. */
             This is the end of the first multiline comment. */
             
3. 整数  
    swift 提供了 `Int32`, `Int16` 等类型，一般情况下还是用 `Int` 和 `UInt` 比较好。可以提高代码可用性。  
    > On a 32-bit platform, Int is the same size as Int32.
On a 64-bit platform, Int is the same size as Int64.

    **除非有特殊要求，一般用 Int 就好，不要用 UInt 类型。**   

4. 浮点数  

   -  Float 是 32 位浮点数  
   -  Double 是 64 位浮点数  

    >Double is preferred.

5. 不同长度的整数之间运算要显式转换 
    
        let twoThousand: UInt16 = 2_000
        let one: UInt8 = 1
        let twoThousandAndOne = twoThousand + UInt16(one)  
6. Type Aliases  
    
        typealias AudioSample = UInt16
7. Tuples  
        元组中的值不一定是类型相同
    
        let http404Error = (404, "Not Found")
        // http404Error is of type (Int, String), and equals (404, "Not Found")
        
    如果不需要，可以把在解析元组时，把不需要的值用 `_` 占位，避免命名烦恼。  
    
        let (justTheStatusCode, _) = http404Error
        print("The status code is \(justTheStatusCode)")
    
    数字下标访问元组  
        
        print("The status code is \(http404Error.0)")
        // Prints "The status code is 404"
        print("The status message is \(http404Error.1)")
        // Prints "The status message is Not Found"
    
    类似字典，用字符串下标访问元组  

        let http200Status = (statusCode: 200, description: "OK")
        print("The status code is \(http200Status.statusCode)")
        // Prints "The status code is 200"

1. Optionals 基本概念  
    Optional 有两种可能：
    1. 有一个值，可以 unwrap 来获得这个值
    2. 根本没有值
    
    Optional 支持所有类型，不仅仅是类；也不需要一个特殊的值来标识值不存在。  
    
1. 把Optional的变量设置nil，可以把它置为没有值的状态

        var serverResponseCode: Int? = 404
        // serverResponseCode contains an actual Int value of 404
        serverResponseCode = nil
        //Nil cannot be assigned to type 'Int'
2. Optional 的变量不初始化，值默认是 nil  
    
        var surveyAnswer: String?
         // surveyAnswer is automatically set to nil
    
    **在 swift 中，nil 不是指向不存在对象的指针，甚至不是指针，它是一个类型的变量没有值时的状态。**  
1. Forced Unwrapping  
    如果确定一个 optional 的值确实有值，可以把 `!` 加到变量后面，叫做 “forced unwrapping of the optional’s value”  
    
        if convertedNumber != nil {
            print("convertedNumber has an integer value of \(convertedNumber!).")
         }

1. Optional Binding  
    如果 Int(possibleNumber) 包含一个值，那么 `actualNumber` 一定有值，所以在第一个分支不需要包含 `!`。  
    在第二个分组使用 `actualNumber` 会报错 “Use of unresolved identifier 'actualNumber'”
    
        if let actualNumber = Int(possibleNumber) {
            print("\"\(possibleNumber)\" has an integer value of \(actualNumber)")
        } else {
            print("\"\(possibleNumber)\" could not be converted to an integer")
        }
1. 可以在一个 `if` 语句进行多个变量的 Optional Binding，只有多个变量同时包含值，语句才为真。  

        if let firstNumber = Int("4"), let secondNumber = Int("42"), firstNumber < secondNumber && secondNumber < 100 {
            print("\(firstNumber) < \(secondNumber) < 100")
        }
        // Prints "4 < 42 < 100"
         
        if let firstNumber = Int("4") {
            if let secondNumber = Int("42") {
                if firstNumber < secondNumber && secondNumber < 100 {
                    print("\(firstNumber) < \(secondNumber) < 100")
                }
            }
        }
        // Prints "4 < 42 < 100"
1. Implicitly Unwrapped Optionals  
    即保证这个变量在使用时，一定是被 unwrapped 了。这样子免去了每次都需要进行 unwrap 的过程。如果一个变量有可能是 nil， 那么最好还是不要定义为这种类型。  
    **注意：如果这样的变量是 nil，那么在使用它时，会产生运行时错误**。  
    
    - 声明  
            
            let possibleString: String? = "An optional string."
            //去掉 ! 会报错：Value of optional type 'String?' not unwrapped; did you mean to use '!' or '?'?
            let forcedString: String = possibleString! // requires an exclamation mark
            
            let assumedString: String! = "An implicitly unwrapped optional string."
            let implicitString: String = assumedString // no need for an exclamation mark
    - 可以 optional binding  
            
            if let definiteString = assumedString {
                print(definiteString)
            }

1. 错误处理  
    - 抛出异常的函数  
        
            func canThrowAnError() throws {
                // this function may or may not throw an error
            }
    - 捕获异常的函数  
        
            func makeASandwich() throws {
                // ...
            }
             
            do {
                try makeASandwich()
                eatASandwich()
            } catch SandwichError.outOfCleanDishes {
                washDishes()
            } catch SandwichError.missingIngredients(let ingredients) {
                buyGroceries(ingredients)
            }
1. 断言  
    - 普通类型    
        
            let age = -3
            assert(age >= 0, "A person's age can't be less than zero.")
            // This assertion fails because -3 is not >= 0.
   - 没有字符串    
    
            assert(age >= 0)
    - 失败的断言  
        
                assertionFailure("A person's age can't be less than zero.")
1. Enforcing Preconditions  
        和断言类似
        
        precondition(index > 0, "Index must be greater than zero.")
        preconditionFailure(_:file:line:)
1. fatalError  
    总是不被忽略，用于开发时标记未实现的分支  

1. assert、precondition、fatalError 区别  
    
    ||    	debug|	release|	release|
    | --- | --- | --- |--- |
    |function|	-Onone|	-O|	-Ounchecked  |
    |assert()	|YES|	NO	|NO  |
    |assertionFailure()|	YES|	NO	|NO**  |
    |precondition()	YES|	YES|	NO  |
    |preconditionFailure()|	YES|	YES	|YES**  |
    |fatalError()*	|YES	|YES|	YES  |



    
    
    
## 参考 
- [The Basics](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID309)
- [Swift asserts - the missing manual](http://blog.krzyzanowskim.com/2015/03/09/swift-asserts-the-missing-manual/)
- [Interesting discussions on Swift Evolution](http://ericasadun.com/2015/12/15/interesting-discussions-on-swift-evolution/)

