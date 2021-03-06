---
title: TCP和UDP对比
date: 2021-11-11
categories:
- computer
tags:
- computer
- tcp
- udp
---

🖥️ TCP（Transmission Control Protocol，传输控制协议）和UDP（User Data Protocol ,用户数据协议）都属于TCP/IP协议簇。两者经常用来进行比较，也是面试的高频题。

下面我们来整理对比下：

<!--more-->

相同点：都属于tcp/ip协议的传输层。

1. 连接性：TCP是面向连接的协议，在收发数据前必须和对方建立可靠的连接，建立连接的3次握手、断开连接的4次挥手，为数据传输打下可靠基础。UDP是一个面向无连接的协议，数据传输前，源端和终端不建立连接，发送端尽可能快的将数据扔到网络上，接收端从消息队列中读取消息段 (只要在应用程序获取的数据前面加上UDP头部，然后交给IP进行发送)。

2. 可靠性：TCP提供可靠交付的服务，传输过程中采用许多方法保证在连接上提供可靠的传输服务，如编号与确认、流量控制、计时器等，确保数据无差错，不丢失，不重复且按序到达。UDP使用尽可能最大努力交付，但不保证可靠交付。

3. 首部开销：TCP报文首部有20个字节，额外开销大。UDP报文首部只有8个字节，标题短，开销小。

4. 传输方式：TCP协议面向字节流，将应用层报文看成一串无结构的字节流，分解为多个TCP报文段传输后，在目的站重新装配。UDP协议面向报文，不拆分应用层报文，只保留报文边界，一次发送一个报文，接收方去除报文首部后，原封不动将报文交给上层应用

5. 连接对象个数：UDP支持一对一、一对多、多对一和多对多的交互通信。TCP只能一对一通信。

6. 使用场景：TCP适合对传输效率要求低，但准确率要求高的应用场景，比如万维网(HTTP)、文件传输(FTP)、电子邮件(SMTP)等。UDP适用于对传输效率要求高，但准确率要求低的应用场景，比如域名转换(DNS)、远程文件服务器(NFS) IP电话、视频会议、直播等。
