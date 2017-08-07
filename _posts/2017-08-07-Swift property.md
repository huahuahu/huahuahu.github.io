---
layout: post
title:  swift Properties
categories: swift
keywords: swift
---

- stored property  
	类、结构体有
- computed property  
	类、结构体、枚举都可以有
- type property
    类、结构体、枚举都可以有  
    
## Stored Properties  
可以是常量，也可以是变量。在定义时可以指定默认值，可以在初始化方法中修改。

	struct FixedLengthRange {
	    var firstValue: Int
	    let length: Int
	}
	var rangeOfThreeItems = FixedLengthRange(firstValue: 0, length: 3)
	// the range represents integer values 0, 1, and 2
	rangeOfThreeItems.firstValue = 6
	// the range now represents integer values 6, 7, and 8
	
### 常量结构体的 Stored Properties  
即使结构体的某个属性是变量，如果结构体的实例是常量，那么这个属性也不能修改值。因为结构体是值类型，而不是引用类型。  

	let rangeOfFourItems = FixedLengthRange(firstValue: 0, length: 4)
	// this range represents integer values 0, 1, 2, and 3
	rangeOfFourItems.firstValue = 6
	// this will report an error, even though firstValue is a variable property
	
### Lazy Stored Properties  
在第一次使用时才计算初始值的属性。可以在声明时用 `lazy` 来修饰。  
**懒加载的属性必须是可变的，而不能是常量。**  
**如果一个属性被 `lazy` 修饰，可能同时被多个线程访问，那么可能会被初始化多次。**

	class DataImporter {
	    /*
	     DataImporter is a class to import data from an external file.
	     The class is assumed to take a non-trivial amount of time to initialize.
	     */
	    var filename = "data.txt"
	    // the DataImporter class would provide data importing functionality here
	}
	 
	class DataManager {
	    lazy var importer = DataImporter()
	    var data = [String]()
	    // the DataManager class would provide data management functionality here
	}
	 
	let manager = DataManager()
	manager.data.append("Some data")
	manager.data.append("Some more data")
	// the DataImporter instance for the importer property has not yet been created
	
## Computed Properties  
类、结构体、枚举都可以定义。提供一个 `getter` 方法，可以选择提供一个 `setter`方法。  

	struct Point {
	    var x = 0.0, y = 0.0
	}
	struct Size {
	    var width = 0.0, height = 0.0
	}
	struct Rect {
	    var origin = Point()
	    var size = Size()
	    var center: Point {
	        get {
	            let centerX = origin.x + (size.width / 2)
	            let centerY = origin.y + (size.height / 2)
	            return Point(x: centerX, y: centerY)
	        }
	        set(newCenter) {
	            origin.x = newCenter.x - (size.width / 2)
	            origin.y = newCenter.y - (size.height / 2)
	        }
	    }
	}
	var square = Rect(origin: Point(x: 0.0, y: 0.0),
	                  size: Size(width: 10.0, height: 10.0))
	let initialSquareCenter = square.center
	square.center = Point(x: 15.0, y: 15.0)
	print("square.origin is now at (\(square.origin.x), \(square.origin.y))")
	// Prints "square.origin is now at (10.0, 10.0)"

### setter 声明的简写形式  
可以用 `newValue` 来代替新的值

	struct AlternativeRect {
	    var origin = Point()
	    var size = Size()
	    var center: Point {
	        get {
	            let centerX = origin.x + (size.width / 2)
	            let centerY = origin.y + (size.height / 2)
	            return Point(x: centerX, y: centerY)
	        }
	        set {
	            origin.x = newValue.x - (size.width / 2)
	            origin.y = newValue.y - (size.height / 2)
	        }
	    }
	}
	
### 只读 Computed Properties  
只声明 `getter` 方法，不声明 `setter` 方法。但是不能用 `let`，要用 `var`，因为值是不确定的。  
可以去掉 `get` 关键字。 

	struct Cuboid {
	    var width = 0.0, height = 0.0, depth = 0.0
	    var volume: Double {
	        return width * height * depth
	    }
	}
	let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
	print("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
	// Prints "the volume of fourByFiveByTwo is 40.0"

## Property Observers
观察属性值的变化，并作出响应。每次调用属性的 `setter` 方法都会响应，即使新的值和旧的值相同。  
可以为 stored properties 增加观察者，但是懒加载属性除外。  
可以为继承属性（stored 或 computed）增加观察者，通过 overriding 属性。  
可以直接在 `set` 方法中写代码，因此不必为当前类声明的 computed property 增加观察者。  

- `willSet` 方法在赋值之前被调用  
- `didSet` 方法在赋值之后被马上调用

`willSet` 方法被传入了一个值叫做 `newValue`，也可以指定这个值的名字。  
 `didSet` 方法被传入了一个值叫做 `oldValue`。如果在 `didSet` 方法为属性赋值，那么这个属性的值会变化。  

	class StepCounter {
	    var totalSteps: Int = 0 {
	        willSet(newTotalSteps) {
	            print("About to set totalSteps to \(newTotalSteps)")
	        }
	        didSet {
	            if totalSteps > oldValue  {
	                print("Added \(totalSteps - oldValue) steps")
	            }
	        }
	    }
	}
	let stepCounter = StepCounter()
	stepCounter.totalSteps = 200
	// About to set totalSteps to 200
	// Added 200 steps
	stepCounter.totalSteps = 360
	// About to set totalSteps to 360
	// Added 160 steps
	stepCounter.totalSteps = 896
	// About to set totalSteps to 896
	// Added 536 steps

当一个有 observer 的值作为 in-out 参数被传入函数时， `willSet` 和 `didSet` 方法总是被低矮用。因为这个变量的值在函数结束时，会被回写。  

### 全局和局部变量  
也可以定义全局和局部的 computed variables，为 stored variables 定义 ovservers。  
全局的常量和变量模式是懒加载的，不需要 `lazy` 修饰。  
局部的常量和变量从来不会是懒加载的。
## Type Properties    
type property 是属于一个类型的变量。无论有多少实例，都只会有一份。  
stored type property 可以是变量或常量，computed type property 总是变量。  
stored type property 一定要有一个默认值，因为类型没有初始化函数。  
stored type property 总是被延时初始化，不必用 `lazy` 修饰；**并且只被初始化一次，即使同时被多个线程访问**。  
### Type Property 语法  
使用 `static` 来修饰 type property。用 `class` 来修饰类的computed type property，以允许子类来 override 父类的实现。  

    struct SomeStructure {
        static var storedTypeProperty = "Some value."
        static var computedTypeProperty: Int {
            return 1
        }
    }
    enum SomeEnumeration {
        static var storedTypeProperty = "Some value."
        static var computedTypeProperty: Int {
            return 6
        }
    }
    class SomeClass {
        static var storedTypeProperty = "Some value."
        static var computedTypeProperty: Int {
            return 27
        }
        class var overrideableComputedTypeProperty: Int {
            return 107
        }
    }
    
### 访问和修改 type property  
    print(SomeStructure.storedTypeProperty)
    // Prints "Some value."
    SomeStructure.storedTypeProperty = "Another value."
    print(SomeStructure.storedTypeProperty)
    // Prints "Another value."
    print(SomeEnumeration.computedTypeProperty)
    // Prints "6"
    print(SomeClass.computedTypeProperty)
    // Prints "27"


