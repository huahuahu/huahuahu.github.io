---
layout: post
title:  Swift Nested Types
categories: swift
keywords: swift
---

Swift 中可以嵌套定义类、结构体和枚举。也支持任意层级的嵌套。  

## 例子  

    struct BlackjackCard {
        
        // nested Suit enumeration
        enum Suit: Character {
            case spades = "♠", hearts = "♡", diamonds = "♢", clubs = "♣"
        }
        
        // nested Rank enumeration
        enum Rank: Int {
            case two = 2, three, four, five, six, seven, eight, nine, ten
            case jack, queen, king, ace
            struct Values {
                let first: Int, second: Int?
            }
            var values: Values {
                switch self {
                case .ace:
                    return Values(first: 1, second: 11)
                case .jack, .queen, .king:
                    return Values(first: 10, second: nil)
                default:
                    return Values(first: self.rawValue, second: nil)
                }
            }
        }
        
        // BlackjackCard properties and methods
        let rank: Rank, suit: Suit
        var description: String {
            var output = "suit is \(suit.rawValue),"
            output += " value is \(rank.values.first)"
            if let second = rank.values.second {
                output += " or \(second)"
            }
            return output
        }
    }
    
    let theAceOfSpades = BlackjackCard(rank: .ace, suit: .spades)
    print("theAceOfSpades: \(theAceOfSpades.description)")
    // Prints "theAceOfSpades: suit is ♠, value is 1 or 11"

## 访问嵌套类型

    let heartsSymbol = BlackjackCard.Suit.hearts.rawValue
    // heartsSymbol is "♡"

## 参考 
- [Nested Types](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/NestedTypes.html#//apple_ref/doc/uid/TP40014097-CH23-ID242)

