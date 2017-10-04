---
layout: post
title: Finding Bugs Using Xcode Runtime Tools  
categories: iOS
keywords: 性能
---

## Main thread checker 
![](http://oda58fqub.bkt.clouddn.com/15071211514455.jpg)    
 
 ![](http://oda58fqub.bkt.clouddn.com/15071212098485.jpg)    
 
 默认打开，在程序运行之后，如果发现有问题，会提示。  
 
## Address Sanitizer  
![](http://oda58fqub.bkt.clouddn.com/15071213208207.jpg)  

可以探测下面的错误  

### Use after free  
![](http://oda58fqub.bkt.clouddn.com/15071213958798.jpg)  

### use after scope  
![](http://oda58fqub.bkt.clouddn.com/15071214738626.jpg)    

### use after return (opt in)    
![](http://oda58fqub.bkt.clouddn.com/15071215119783.jpg)  

### Malloc Scribble  
获知变量 alloc 和 dealloc 时的堆栈。   
![](http://oda58fqub.bkt.clouddn.com/15071220210287.jpg)    

##  thread sanitizer  
![](http://oda58fqub.bkt.clouddn.com/15071223231666.jpg)  

### 可以检测基于 collection 的 data race    
`NSMutableArray` 和 `NSMutableDictionary` 。  
![](http://oda58fqub.bkt.clouddn.com/15071225121419.jpg)     


### Swift access  race ？？？   
![](http://oda58fqub.bkt.clouddn.com/15071230681345.jpg)  

  
## Undefined Behavior Sanitizer     
### singed integer overflow
![](http://oda58fqub.bkt.clouddn.com/15071235217776.jpg)


### Alignment Violation  
![](http://oda58fqub.bkt.clouddn.com/15071239969334.jpg)  

整数是 4 字节对齐的。  
如何解决？使用 `memcpy()` 来解决。  
![](http://oda58fqub.bkt.clouddn.com/15071240358420.jpg)  

###  Nonnull Return Value Violation  
![](http://oda58fqub.bkt.clouddn.com/15071243385685.jpg)

### 如何开启 
![](http://oda58fqub.bkt.clouddn.com/15071243656396.jpg)

## 工具对系统负载的影响  
![](http://oda58fqub.bkt.clouddn.com/15071246587129.jpg)


## 参考  
- [Finding Bugs Using Xcode Runtime Tools](https://developer.apple.com/wwdc17/406)
- [(转)offsetof与container_of宏[总结]](http://www.cnblogs.com/woainilsr/p/3472409.html)

