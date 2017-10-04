---
layout: post
title: System Trace in Depth
categories: iOS
keywords: 性能
---


## 原理  
使用 system trace 时，会记录最近 5s 的 kernel trace，然后分析下面的操作：  

1.  Scheduling activity  
2.  System calls  
3.  Virtual memory operations      

## 使用  
在代码里插入相应的语句，当运行到这里是，会在 Instrument 的结果中。    
插入的语句可以指明一个事件的发生，或者一个时间段的起始和结束。其中第一个参数指定事件的 ID，  是一个整数。

    //某个事件的发生
    // Emit a signpost for Instruments
    kdebug_signpost(5, 0, 0, 0, 0)  
    
    //事件段的起始和结束
    // Timing an activity (code 10 - "Start Up")
    kdebug_signpost_start(10, 0, 0, 0, 0);
    [self loadAssets];
    kdebug_signpost_end(10, 0, 0, 0, 0);
  
## 查看感兴趣的点的统计信息
可以在 5 个选项之间查看。
![](http://oda58fqub.bkt.clouddn.com/15071075460143.jpg)  

如果选择第四个，可以看到统计信息，包括最小持续时间、平均时长、最多持续时间等。

![](http://oda58fqub.bkt.clouddn.com/15071048071642.jpg)  

选择列表中的某个点以后，右键选择，可以过滤掉其他时间的信息。  
![](http://oda58fqub.bkt.clouddn.com/15071049938590.jpg)  

## 查看线程信息  
选中某一个线程以后，可以选择看出线程的信息。    
![](http://oda58fqub.bkt.clouddn.com/15071078326087.jpg)    
Narrative 按照时间顺序给出了线程调度的信息。  
![](http://oda58fqub.bkt.clouddn.com/15071079487968.jpg)    
 Summary: Thread State 给出了线程处于各个状态的统计信息。  
 
 time profile 只是查看 CPU 的执行情况，如果一个线程长时间得不到调度，在 time profile 里得不到相应的信息。
## System Load    
可以看到各个时刻的线程的状态。  
![](http://oda58fqub.bkt.clouddn.com/15071083081172.jpg)    

## User Interactive Load Average
统计的是 10ms 内，线程优先级大于33 以及 QoS 是 User Interactive Class 的线程数目。  
当数目大于 CPU 数目时，会标记为橙色。    
![](http://oda58fqub.bkt.clouddn.com/15071084431838.jpg)    

## 参考
- [System Trace in Depth](https://developer.apple.com/videos/play/wwdc2016/411/)
- [System Trace入坑笔记 - WWDC](http://www.jianshu.com/p/6629dff8a2dc)

