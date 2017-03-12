---
title: TCP/IP协议之Traceroute程序    
categories: network  
tag:    
- tcp/ip
---
  
## 一、原理  
Traceroute发送一份UDP数据报给目的主机，但它选择一个不可能的值作为UDP端口号，使得目的主机的任何一个应用程序都不可能使用该端口。
起始时，数据报的TTL字段是1，然后每次把TTl字段依次加1，以确定路径中的每个路由器。
每个路由器在丢弃的UDP数据报时都返回一个**ICMP超时报文**，而最终主机产生一个ICMP端口不可达的报文。  
## 二、ICMP超时报文格式  
![](http://oda58fqub.bkt.clouddn.com/14892922950061.jpg)


## 三、Traceroute程序  
1. 不能保证现在的路由就是将来所采用的路由，甚至连续两份的IP数据报都可能采用不同的路由。  
2. 不能保证ICMP报文的路由与traceroute程序发送的UDP数据报采用同一个路由。  
3. 返回的ICMP报文中的信源IP地址是UDP数据报到达的路由器接口的IP地址，而不是路由器发送接口的地址。



