---
layout: post
title:  Swift Type Casting
categories: swift
keywords: swift
---

检查实例的类型，或者把一个实例当做子类或父类的实例。  
在 Swift 中，用 `as` 和 `is` 来做判断。  
## 定义几个类   

    class MediaItem {
        var name: String
        init(name: String) {
            self.name = name
        }
    }
    
    class Movie: MediaItem {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}
 
    class Song: MediaItem {
        var artist: String
        init(name: String, artist: String) {
            self.artist = artist
            super.init(name: name)
        }
    }
    
    let library = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley"),
    Movie(name: "Citizen Kane", director: "Orson Welles"),
    Song(name: "The One And Only", artist: "Chesney Hawkes"),
    Song(name: "Never Gonna Give You Up", artist: "Rick Astley")
    ]
    // the type of "library" is inferred to be [MediaItem]

## 检查类型  
`is` 操作符，如果是特定子类型，那么返回 `true`，否则返回 `false`。

    var movieCount = 0
    var songCount = 0
     
    for item in library {
        if item is Movie {
            movieCount += 1
        } else if item is Song {
            songCount += 1
        }
    }
     
    print("Media library contains \(movieCount) movies and \(songCount) songs")
    // Prints "Media library contains 2 movies and 3 songs"
    
## 向下转型  
某类型的一个常量或变量可能实际上属于一个子类。当确定是这种情况时，你可以尝试向下转到它的子类型。用 `as` 来进行这个操作。 `as?` 返回一个可选值； `as!` 返回一个值，或者产生一个运行时错误。
    
    for item in library {
        if let movie = item as? Movie {
            print("Movie: \(movie.name), dir. \(movie.director)")
        } else if let song = item as? Song {
            print("Song: \(song.name), by \(song.artist)")
        }
    
## 为 `Any` 和 `AnyObject` 进行类型转换 
Swift 提供了两个特殊的类型：  

- `Any` 代表任何类型的实例，包括函数类型。
- `AnyObject` 代表任何类的实例。  

下面是一个使用 `Any` 的例子。这个数组可以加入任何类型的数据。

    var things = [Any]()
     
    things.append(0)
    things.append(0.0)
    things.append(42)
    things.append(3.14159)
    things.append("hello")
    things.append((3.0, 5.0))
    things.append(Movie(name: "Ghostbusters", director: "Ivan Reitman"))
    things.append({ (name: String) -> String in "Hello, \(name)" })

Swift 中，`Any` 可以表示任何类型，包括可选值。如果需要把可选值作为 `Any` 使用，可以用 `as` 进行显式转换。

    let optionalNumber: Int? = 3
    things.append(optionalNumber)        // Warning
    things.append(optionalNumber as Any) // No warning

## 参考  
- [Type Casting](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TypeCasting.html#//apple_ref/doc/uid/TP40014097-CH22-ID338)


