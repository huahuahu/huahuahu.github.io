---
title: TCP/IP协议之ICMP   
categories: network  
tag:    
- tcp/ip
---
  
## 一、概述  
Ping程序是对两个TCP/IP系统连通性进行测试的基本工具。该程序发送一份ICMP回显请求报文给主机，并等待返回ICMP回显应答。  
## 二、格式  
大多数TCP/IP实现都在内核中直接支持Ping服务器——**这种服务器不是一个用户进程**。  
![](http://oda58fqub.bkt.clouddn.com/14892897294014.jpg)  

在Unix中，把ICMP的标识符字段设置为发送进程的ID号，这样子即使在同一台主机上同时运行了多个Ping程序实例，也能正确识别出返回的信息。  


