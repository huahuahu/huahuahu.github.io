---
layout: post
title: WWDC - Advanced Debugging and the Address Sanitizer
categories: iOS
keywords: debug
---


## A debug trick  
在异常端点处运行 `po $arg1`，找出异常信息。  

![](http://oda58fqub.bkt.clouddn.com/15071680163474.jpg)  
    
##  Address Sanitizer
### 概述 
1. 是一个运行时检测工具  
2. 发现内存问题 
3. 可以用于模拟器和设备 

### 可以发现的问题  

1. Use after free
2. Heap buffer overflow
3. Stack buffer overflow
4. Global variable overflow
5. Overflows in C++ containers
6. Use after return

### 原理  
当打开这个功能时，在编译时传入了一个参数，在运行时链接了一个动态库 `asan dylib`。  
![-w600](http://oda58fqub.bkt.clouddn.com/15071701147234.jpg)  

系统为所有内存维护了一个 shadow memory，标记了那些是可以正常使用的，那些是不可以访问的。  
 ![-w600](http://oda58fqub.bkt.clouddn.com/15071702779027.jpg)  
 在上图中，红色区域是不可以访问的，因为不是预分配的地址。  
 为了达到这个目的，系统预留了部分内存地址做为 shadow memory，把每 8 个字节的状态用 1 个比特来代表。    
 ![-w244](http://oda58fqub.bkt.clouddn.com/15071706174126.jpg)    
在访问内存时，只需要做一个偏移来查看比特位的状态即可。  

    bool IsPoisoned(Addr) { 
        Shadow = Addr >> 3  +  Offset 
        return (*Shadow) != 0 
    }

### 检测堆的内存错误原理  
复写了系统的 `malloc` 函数，把分配好的内存区域周围标记为 posioned。这样子在越界时，可以检测出来。  
![](http://oda58fqub.bkt.clouddn.com/15071708543931.jpg)  
可以检测出来下面的错误：  

- Heap underflows/overflows 
- Use-after-free 
-  double free### 检测栈上内存错误  
类似，在栈上分配的内存周围标记为 posioned，并在访问内存之前做检查。  
  ![-w600](http://oda58fqub.bkt.clouddn.com/15071709662042.jpg)  
这样子也可以检测出全局变量内存错误。  

### 复写了很多内存函数  
不限于 memcpy, memset, strcpy, strlen, fwrite, printf, getline, ...  

### 增加的负载 
![](http://oda58fqub.bkt.clouddn.com/15071711092712.jpg)






  

