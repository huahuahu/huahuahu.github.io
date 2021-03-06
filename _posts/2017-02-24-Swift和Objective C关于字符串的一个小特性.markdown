---
categories: characters  
tag:  
- swift  
- unicode

---
## 一、Unicode的一个小特性  

首先，Unicode规定了许多code point，每一个code point表示一个字符。如\u0033表示字符“3”，\u864e表示字符“你”。  
反过来，不是每一个字符都对应一个code point，每一个字符也不止有一个code point的表示方法。
比如说，“🐯”这个emoji表情对应的code point是“\ud83d\udc2f\u000d\u000a”，由4个code point组成，而不是一个。  
“é”这个字符对应的code point有两个，“\u00e9”以及“\u0065\u0301”这两个code point序列（一个或多个code point）均可表示这个字符。  
那么如何比较两个字符串是否相同呢？Unicode规定了[正规化的方法](http://www.unicode.org/reports/tr15/)，要把code point的序列正规化，然后判断是否一致。  
下面我们看下Swift和NSString对这个规则的支持情况。

## 二、Objective C中的字符串
> An NSString object encodes a Unicode-compliant text string, represented as a sequence of UTF–16 code units

NSString支持Unicode，一个NSString其实是UTF-16编码以后的得到的code unit序列，而`length`属性返回的是code unit序列的长度，而不是字符的长度。
> The number of UTF-16 code units in the receiver.


```
    NSString *str1 = @"🐯";
    NSLog(@"str1: %@,length is %zd",str1,str1.length);
```    

输出如下
    
    str1: 🐯,length is 2
> The comparison uses the canonical representation of strings, which for a particular string is the length of the string plus the UTF-16 code units that make up the string. 

比较字符串时，也只是比较code unit的序列，因此没有用到Unicode归一化的表达，可能会造成不同code point表示的同一个字符被认为是不同的字符。
  
    
```
    str1 = @"\u00e9";
    NSString *str2 = @"e\u0301";
    NSLog(@"\n str1: %@, length %zd;\n str2: %@, length %zd;\n str1 equal to str2 %@",str1,str1.length, str2, str2.length, ([str2 isEqualToString:str1] ? @"yes" : @"no"));
```
输出如下

    str1: é, length 1;
    str2: é, length 2;
    str1 equal to str2 no
    
## 三、Swift中的字符串
> A string is a series of characters 

在Swift关于String的文档中，第一句话就是字符串是字符的序列，而不是code unit的序列。String有一个`characters`的属性，是字符的集合。


```
    let str1 = "cafe\u{301}"
    let str2 = "caf\u{e9}"
    print("str1 is \(str1),length is ",str1.characters.count,"; str2 is \(str2), stre length is",str2.characters.count)
```
结果如下：

    str1 is café,length is  4 ; str2 is café, stre length is 4
    
在比较字符串时，结果符合将code point正规化之后的结果。


```
           print("str1 is equal to str2",str1 == str2)
```

结果如下

    str1 is equal to str2 true




