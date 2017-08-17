---
layout: post
title:  Swift Access Control
categories: swift
keywords: swift
---

访问控制可以隐藏实现的细节。  
## 模块和源文件
Swift 访问控制是基于模块和源文件实现的。  
模块是指独立的代码单元，比如 framework 或 application，可以单独构建和发布。可以用 `import` 关键字来导入。  
每一个构建的 target 都是一个单独的模块。把 一个 framework 引入到应用或另一个 framework 中时，这 framework 中所有定义的东西，都属于同一个模块。    
源文件是一个模块中的单独一个 Swift 源代码文件。一个源代码文件中可以定义多个类型、函数等。  
## 访问级别  
Swift 定义了 5 个访问级别。  

- open 和 public    
    可以被当前模块的所有源文件访问，也可以被引入这个模块的其他模块访问。  
- internal  
    可以被当前模块的所有源文件访问，不可以被模块外的源文件访问。  
- file-private    
    只有在当前源文件中的代码才可以访问。
- private  
    只有在当前定义的作用域中可以使用，以及同一个源文件中定义的扩展。  

open 是最高的访问级别， private 是最低的访问级别。  
open 只能应用于类和类成员，和 public 的区别如下：  

- public 以及更低访问级别的类只能在其定义的模块中被子类化。  
- public 以及更低访问级别的类成员，只能在其定义的模块中被重写。  
- open 的类，可以在其定义的模块以及引入这个模块的模块中被子类化。  
- open 的类成员，可以在其定义的模块以及引入这个模块的模块中被重写。  

### 访问控制的原则  
> “No entity can be defined in terms of another entity that has a lower (more restrictive) access level.”  
> 
> “不可以在某个实体中定义访问级别更低（更严格）的实体。”    

- 一个 public 的变量，其类型不能是 interna、file-private 或 private。  
- 一个函数的访问级别，不能高于参数类型或返回值类型的访问级别。  



### 默认访问级别 
如果不显式声明，所有定义实体的访问级别都是 internal。 
### 单元测试的target的访问级别  
如果用 `@testable` 来修饰，那么定义的实体可以被测试的 target 访问。  
## 访问控制语法  

    public class SomePublicClass {}
    class SomeInternalClass {}//implicitly internal
    fileprivate class SomeFilePrivateClass {}
    private class SomePrivateClass {}
    
    public var somePublicVariable = 0
    let someInternalConstant = 0//implicitly internal
    fileprivate func someFilePrivateFunction() {}
    private func somePrivateFunction() {}
    
## 自定义类型  
定义类型的访问控制级别会影响到成员的访问控制级别。  
如果定义类型的访问控制级别是 private 或 file-private，那么所有成员的默认访问控制级别也是 private 或 file-private。  
**如果一个类型定义为 public，那么成员变量的默认访问级别是 internal。**  

    public class SomePublicClass {                  // explicitly public class
        public var somePublicProperty = 0            // explicitly public class member
        var someInternalProperty = 0                 // implicitly internal class member
        fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
        private func somePrivateMethod() {}          // explicitly private class member
    }
    
## 元组类型  
元组的访问级别是其元素的最低访问级别。  
如果元组中元素的访问级别分别是 private 和 public，那么元组的访问级别是 private。

## 函数类型  
函数的访问级别默认是参数和返回值中最低的访问级别。也可以指定更低的访问级别。如果当前上下文的默认访问级别和计算得到的访问级别冲突，需要显式地说明。  

    //不加 private 编译不过
    private func someFunction1() -> (SomeInternalClass, SomePrivateClass) {
        // function implementation goes here
        return (SomeInternalClass(),SomePrivateClass())
    }
##  枚举类型  
每一个枚举值的访问级别和枚举类型的访问级别一致，不能特殊指定。  

    public enum CompassPoint {
        case north
        case south
        case east
        case west
    }
    
在枚举定义中的原始值和关联值的访问级别至少要不低于枚举类型的访问级别。  
不能在 internal 的枚举类型中使用 private 的关联值。  

## 嵌套类型    
在 private 的类型中的嵌套类型，默认是 private 类型，以此类推。  
在 public 的类型中定义的嵌套类型，默认是 internal 类型。  
## 子类   
子类的访问级别不能高于父类。比如把 internal 的类子类化为 public。  
可以在子类中把父类的某个成员的访问控制级别提高。  

    public class A {
        fileprivate func someMethod() {}
    }
     
    internal class B: A {
        override internal func someMethod() {}
    }

可以在子类中调用父类的访问控制级别更高的函数。  

    public class A {
        fileprivate func someMethod() {}
    }
     
    internal class B: A {
        override internal func someMethod() {
            super.someMethod()
        }
    }

## 常量、变量、属性和下标  
常量、变量、属性的访问级别不能比类型更高。比如不能把 private 类型的变量声明为 public。  
下标的访问级别不能比索引类型或返回类型更高。  
如果常量、变量、属性或下标使用了 private 类型，那么必须声明为 private。

    //不用private 会报错
    //Variable must be declared private or fileprivate because its type 'SomePrivateClass' uses a private type
    private var privateInstance = SomePrivateClass()  
    
## getter 和 setter  
可以为 getter 和 setter 赋予不同的访问级别，来现在读写的访问级别。  

    struct TrackedString {
        private(set) var numberOfEdits = 0
        var value: String = "" {
            didSet {
                numberOfEdits += 1
            }
        }
    }
    
也可以同时设置 getter 和是setter 的访问级别。  

    public struct TrackedString {
        public private(set) var numberOfEdits = 0
        public var value: String = "" {
            didSet {
                numberOfEdits += 1
            }
        }
        public init() {}
    }
    
## 构造器  
构造器的访问级别可以被定义为比类型的访问级别更低。 required 构造器的访问级别必须和类型的访问级别一致。  
## 默认构造器  
默认构造器的访问级别和类型的访问级别一致。  
如果想让一个 public 的类在另一个模块中可以通过不带参数的构造器创建，需要显式声明一个构造器。  
### 结构体的 Memberwise 构造器  
和所有成员访问级别中最低的一致。  
## 协议  
协议中每一个要求的访问级别和协议本身相同，不能把某个要求的访问级别设置为何协议本身不一致。  
**如果定义了 public 的协议，那么所有要求都是 public 而不是 internal 的。**  
### 协议继承  
继承得到的协议的访问级别不能高于父协议的访问级别。 
### 遵守协议  
一个类型可以遵守访问级别比它低的协议。比如可以使一个 public 的类型遵守一个 internal 的协议。只能在协议所在的模块中作为符合该协议的类型来使用。  
类型遵守协议的上下文中，访问级别是协议和类型的最低级别。比如一个 public 的类型遵守一个 internal 的协议，那么当前上下文的访问级别是 internal。  
> 如果你采纳了协议，那么实现了协议的所有要求后，你必须确保这些实现的访问级别不能低于协议的访问级别。例如，一个 public 级别的类型，采纳了 internal 级别的协议，那么协议的实现至少也得是 internal 级别。

## 扩展
扩展的访问级别和原有类型的访问级别一致。如果扩展一个 private 类型，那么扩展中的成员默认有 private 的访问控制。  
如果使用扩展来实现协议，那么不可以显示指定这个扩展的访问级别。
>协议拥有相应的访问级别，并会为该扩展中所有协议要求的实现提供默认的访问级别  

### 扩展中的私有成员  
同一个文件中的扩展，可以视为所有代码都写在原始声明中。

    protocol SomeProtocol {
        func doSomething()
    }
    struct SomeStruct {
        private var privateVariable = 12
    }
     
    extension SomeStruct: SomeProtocol {
        func doSomething() {
            print(privateVariable)
        }
    }
    
## 泛型  
> 泛型类型或泛型函数的访问级别取决于泛型类型或泛型函数本身的访问级别，还需结合类型参数的类型约束的访问级别，根据这些访问级别中的最低访问级别来确定。

## 类型别名 
定义的任何类型别名都被看做一个新的类型，以便进行访问控制。  
类型别名的访问级别不能高于原始类型的访问级别。  
private 类型别名可以用 private、public 类型来定义。public 的类型别名不能用 private 的类型来定义。

## 参考 
- [Access Control](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html#//apple_ref/doc/uid/TP40014097-CH41-ID3)
- [访问控制](http://wiki.jikexueyuan.com/project/swift/chapter2/24_Access_Control.html)



