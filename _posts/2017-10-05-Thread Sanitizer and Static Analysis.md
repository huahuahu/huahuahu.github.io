---
layout: post
title: WWDC - Thread Sanitizer and Static Analysis  
categories: iOS
keywords: debug
---

## Thread Sanitizer 过程    
编译过程中链接了一个新的库。  
![](http://oda58fqub.bkt.clouddn.com/15071278971652.jpg)  

也可以通过命令行来操作：  

    $ clang -fsanitize=thread source.c -o executable
    $ swiftc -sanitize=thread source.swift -o executable
    $ xcodebuild -enableThreadSanitizer YES  

**不支持设备，只支持模拟器！！！** 如果在设备上运行，无法选中。   
![](http://oda58fqub.bkt.clouddn.com/15071282328734.jpg)

## 原理 
类似 vector clock 的技术。    
对每 8 个字节，分配一个叫做 Shadow state 的东西，记录**最多 4 个线程**对这块内存的访问记录。  
同时，每个线程存储一个结构，包括  

- 线程自己的时间戳 
- 其他线程的时间戳，用来构建同步点  
- 每次内存访问，会增加时间戳的值  

![](http://oda58fqub.bkt.clouddn.com/15071654743394.jpg)  

### 一个例子  
1. 线程 1 写    
    先获取一个锁，然后把自己的时间戳从 2 变为 3。  
    然后把内容写入内存中，把相关信息写入 Shadow 中。
    ![](http://oda58fqub.bkt.clouddn.com/15071663123966.jpg)  
    线程 1 在释放锁之前，会根据自己时间戳更新锁，这样子锁就有了线程 1 的时间戳信息。
2.  线程 2 写    
    首先，获取锁。把自己线程的时间戳从 22 增加为 23，然后根据锁中线程 1 的时间戳信息，更新自己线程中线程 1 的时间戳为 3。然后访问内存，并把自己线程的时间戳信息写入 shadow。
    ![](http://oda58fqub.bkt.clouddn.com/15071665041468.jpg)  
2. 线程 2 校验    
    此时比较 shadow 中线程 1 的时间戳信息和线程 2 中线程 1 的时间戳信息，发现没有问题，校验成功。  
    然后更新锁中关于线程 2 的信息，释放锁。
    ![](http://oda58fqub.bkt.clouddn.com/15071666918580.jpg)  
    
3. 线程 3 访问    
    线程 3 访问时没有获取锁，而是直接写入内存。由于没有获取锁，所以线程 3 中关于其他线程的时间戳信息没有更新。在比较 shadow 中其他线程的时间戳信息时，发现 shadow 中的时间戳比线程 3 中的时间戳大，因此认为发生了 data race。
    ![](http://oda58fqub.bkt.clouddn.com/15071668145033.jpg)


## Static Analyzer
### 开启方法
![](http://oda58fqub.bkt.clouddn.com/15071294529501.jpg)  

### Find Missing Localizability  
![](http://oda58fqub.bkt.clouddn.com/15071670762098.jpg)  
### Nullability Violations  
![](http://oda58fqub.bkt.clouddn.com/15071671152737.jpg)
## 参考 
-[Thread Sanitizer and Static Analysis  ](https://developer.apple.com/wwdc16/412)
- [Valgrind Home](http://valgrind.org/)
- [Vector Clock理解](http://blog.csdn.net/yfkiss/article/details/39966087)


