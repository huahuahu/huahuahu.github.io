---
layout: post
title: Understanding Undefined Behavior  
categories: iOS
keywords: debug
---

> “undefined behavior: behavior for which this International Standard imposes no requirements.”   

##  example of  Undefined Behavior 
###  Use of an uninitialized variable
![](http://oda58fqub.bkt.clouddn.com/15071255586026.jpg)

### Misaligned pointers    
![](http://oda58fqub.bkt.clouddn.com/15071257365585.jpg)  

### Access to an object past end of lifetime  
![](http://oda58fqub.bkt.clouddn.com/15071257567494.jpg)  

##  The Compiler and Undefined Behavior  
为了提高效率，编译器有做了一些假设，如下是几个例子。  
![](http://oda58fqub.bkt.clouddn.com/15071259587970.jpg)  

### 编译器不同优化会引起不同的结果    
#### 优化1，先去掉多余的 NULL 检查，再去掉多余的语句  
![](http://oda58fqub.bkt.clouddn.com/15071261052609.jpg)  

#### 优化2，先去掉多余的语句，再去掉多余的 NULL 检查  
![](http://oda58fqub.bkt.clouddn.com/15071261419230.jpg)  

这样子，仅仅是不同的优化顺序，最终得到的结果是不同的。  
![](http://oda58fqub.bkt.clouddn.com/15071261871376.jpg)    

此外，还有不同的优化等级、不同的设备、不同的编译器版本等。  
## Undefined Behavior 的特征 

1. 不可预测  
2. 可能影响整个程序  
3. 会导致潜伏的 bug  
4. 是很多安全问题的原因  

## 如何解决 
### 工具篇  
- Compiler  
- Static Analyzer  
- Address Sanitizer  
- Thread Sanitizer  
- Undefined Behavior Sanitizer  

### tips 
- Modernize your project (Editor → Validate Settings)  
   ![](http://oda58fqub.bkt.clouddn.com/15071264683846.jpg)  
   
- Run the Static Analyzer    
    把开关打开。
    ![](http://oda58fqub.bkt.clouddn.com/15071265707005.jpg)  
    
-   Use the Runtime Sanitizers    
    不同的工具可以解决不同的问题。
    ![](http://oda58fqub.bkt.clouddn.com/15071266535886.jpg)  
    
## 参考   
- [Understanding Undefined Behavior](https://developer.apple.com/wwdc17/407)
    