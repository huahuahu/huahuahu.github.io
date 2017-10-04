---
layout: post
title: Using Time Profiler in Instruments
categories: iOS
keywords: 性能
---



要用 release 版本来profile  

## 概述
time profile 是使用采样的方法来统计，而不是记录每一个方法调用的起始和结束，采样间隔是 1 ms。    
![](http://oda58fqub.bkt.clouddn.com/15070954055160.jpg)  

在上图中，`main` 函数被采样了 5 次， `method3` 没有被采样，但是确实执行了。  
不能区分长时间运行的任务和重复执行的任务。  
关注点是 CPU，而且不会记录所有操作。比如 `method3` 没有被采样。 
## 如何看 time profile 结果  
![](http://oda58fqub.bkt.clouddn.com/15070958899349.jpg)  

最左边的时间，不是实际耗费的时间，而是该方法被采样的次数乘以采样间隔。  
self weight 指的是该函数没有调用其他函数时，被采用国内的次数乘以采样间隔。  
上面的图中，选中的那行， weight 和 self weight 都是 117 ms，说明该函数没有调用其他函数。  

可以看到各个线程、各个 CPU core 的使用情况。  
![](http://oda58fqub.bkt.clouddn.com/15070983757784.jpg)  

注意不要阻塞主线程，主线程的 CPU 使用率不能太高。




## 参考 
- [Using Time Profiler in Instruments](https://developer.apple.com/videos/play/wwdc2016/418/)

 

