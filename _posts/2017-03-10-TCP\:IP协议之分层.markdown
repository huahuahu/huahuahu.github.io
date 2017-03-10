---
title: TCP/IP协议之分层  
categories: network  
tag:    
- tcp/ip
---
![](http://oda58fqub.bkt.clouddn.com/14891227495745.jpg)    

1. 应用层和运输层只在端系统（End System）中实现， 底层协议在中间系统（Intermediate System）实现  
2. ICMP和IGMP属于网络层的附属协议。虽然其内容是IP数据报的载荷（Payload），但是功能上讲属于网络层。  
3. ARP和RARP是链路层协议  
在逻辑上，这两个是链路层协议，但是实际上和IP数据包一样，有各自的以太网数据帧类型。





