---
title: TCP Server有两个套接字  
categories: network  
tag:    
- 链路层

---

![-w500](http://oda58fqub.bkt.clouddn.com/14936349565086.jpg)  
TCP服务器有一个特殊的套接字，欢迎运行在任意主机上的客户进程的某些初始接触。  
三次握手期间，客户进程敲服务器的欢迎之门。该服务器“听到”敲门时，它将生成一个新的TCP套接字对象。它专门对客户进行连接的新生成的套接字，称为**连接套接字 (connection socket)**。  
初次遇到TCP套接字的学生有时会混淆欢迎套接字（所有要与服务器通信的客户的起始接触点）和每个新生成的服务器侧的连接套接字（随后为每个客户通信而生成的套接字）。  
## 例子  
    connectionSocket, addr = serverSocket.accept()
    sentence = connectionSocket.recv(1024)  

`serverSocket`是欢迎套接字，`connectionSocket`是连接套接字。  
## 来自  
1. JamesF.Kurose, KeithW.Ross, 库罗斯,等. 计算机网络:自顶向下方法[M]. 高等教育出版社, 2009.



