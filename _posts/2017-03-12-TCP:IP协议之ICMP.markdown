---
title: TCP/IP协议之ICMP   
categories: network  
tag:    
- tcp/ip
---
  
## 概述  
一般认为是IP层的一个组成部分，用户传递差错报文及其他需要注意的信息。  
![](http://oda58fqub.bkt.clouddn.com/14892832316726.jpg)

ICMP报文在IP数据报内部被传输。  
![](http://oda58fqub.bkt.clouddn.com/14892832502670.jpg)  
ICMP报文格式如上，所有报文前四个字节的格式都是相同的。  
## 二、ICMP报文类型  
![](http://oda58fqub.bkt.clouddn.com/14892833499037.jpg)

为了不产生广播风暴，下面的情况不会产生ICMP差错报文：

1. ICMP差错报文（ICMP查询报文可能会产生ICMP差错报文）  
2. 目的地址是广播地址或多播地址的IP数据报  
3. 作为链路层广播的数据报  
4. 不是IP分片的第一片  
5. 源地址不是单个主机的数据报。  

发送一份ICMP差错报文时，报文始终包含IP的首部和产生ICMP差错报文的IP数据报的前8个字节。根据IP数据报首部的协议字段，接收方可以把报文和特定的协议对应起来；根据IP数据报前8个字节中的TCP或UDP报文首部中的端口号，可以把数据报和用户进程联系起来。  

# 三、ICMP时间戳请求与应答  
允许系统向另一个系统查询当前的时间，返回的建议值是自午夜开始计算的**毫秒数**。  
![](http://oda58fqub.bkt.clouddn.com/14892875250892.jpg)  

![](http://oda58fqub.bkt.clouddn.com/14892877651671.jpg)

假设RTT是正确的，且假设RTT的一半用于请求报文的传输，那么可以调整本机的时间。  

## 四、ICMP端口不可达差错  
![](http://oda58fqub.bkt.clouddn.com/14892881618052.jpg)  

ICMP报文是主机和主机之间交换的，并未指定端口号。

- ICMP报文中的数据部分包含“产生差错的数据报IP首部”是因为IP首部中包含了协议字段，使得ICMP可以知道如何解释后面8个字节。  
- UDP首部的前8个字节是源端口和目的端口的端口号。接收ICMP的系统可以根据源端口号把差错报文和某个特定的用户进程相关联。  

![](http://oda58fqub.bkt.clouddn.com/14892884303627.jpg)



