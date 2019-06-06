---
title: 看了那么多，TCP/IP究竟是什么（一）
comments: true
top: false
date: 2019-06-04 16:27:47
categories: tcp
tags: tcp
img: https://qiniu1.lxfriday.xyz/WaterM/b99c7b46-c8cb-4737-80ae-a3e1c5d1f646_WX20190531-142955-tcp.png
---

我们通常理解的 TCP/IP 可能是单一协议，实则不然，TCP/IP 是一个协议族，它里面包含有负责各个方面的协议。简单来说，TCP/IP 是利用 IP 进行通信时所必须用到的协议群的统称。

互联网是由许许多多的网络通信技术融合而成的，而 TCP/IP 是其中极其重要的一部分。

20世纪70年代，ARPANET 中的一个机构研发出了 TCP/IP 并在 1982 年正式确定了 TCP/IP 的具体规范，Unix 是最早实现了 TCP/IP 协议。

IP 、 ICMP、TCP或 UDP、HTTP等都属于 TCP/IP 协议。他们与 TCP 或者 IP 的关系紧密，TCP/IP 泛指这些协议，他们也被称为 TCP/IP 协议族。

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/0b19df21-8d14-4681-ae0d-12b1e4413e6f_WX20190604-203747-tcpip协议群.png" alt="TCP/IP 协议群"><p class="img-desc">TCP/IP 协议群</p></div>

为了促进协议的标准化，ISO 指定了一个国际标准 OSI。OSI 被划分为 7个层次，每个层次都有自己的功能，TCP 属于第4层传输层。

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/77f49be4-35ed-428f-8f21-5c6dfc153745_WX20190604-200035-osi参考模型.png" alt=""><p class="img-desc"></p></div>

TCP，是 Transmission Control Protocol  的缩写，中文叫传输控制协议。正如名字中的传输和控制一样，它能充分的进行传输控制，它能在丢包时自动检测到并进行重发，还可以对乱序的包进行排序拼接（seq,ack）。同时 TCP 是一种面向有连接的协议，什么叫有连接呢，就是必须和对方建立起连接才会进一步的发送数据进行交互，这一点区别于 UDP。

传输过程中数据可能会出现损坏、丢失、重复、排序混乱的情况，TCP 通过校验和、序列号、确认应答、重发控制、连接管理以及窗口控制等机制实现可靠性传输。

TCP 根据应用的需要，负责建立连接、发送数据以及断开连接。TCP 提供将应用层发来的数据顺序发送至对端的可靠传输。

英文缩写解释
- TCP: Transmission Control Protocol 传输控制协议
- IP: Internet Protocol 网际协议
- UDP: User Datagram Protocol 用户数据报协议
- HTTP: HyperText Transfer Protocol 超文本传输协议
- SMTP: Simple Mail Transfer Protocol 简单邮件传输协议 
- FTP: File Transfer Protocol 文件传输协议
- Telnet: Internet 远程登陆服务的标准协议
- SNMP: Simple Network Management Protocol 简单网络管理协议
- ICMP：Internet Control Message Protocol 互联网控制报文协议
- ARP: Address Resolution Protocol 地址解析协议
- RIP: Routing Information Protocol 路由信息协议，是一种动态路由选择协议
- OSPF: Open Shortest Path First 开放式最短路径优先
- BGP: Border Gateway Protocol 边界网关协议
- ARPANET: Advanced Research Projects Agency Network 美国高级研究计划署
- ISO: 国际标准化组织
- OSI: 开放式通信系统互联参考模型
- RFC：Request For Comment 是一系列以编号排定的文件，几乎所有的因特网标准都收录在RFC文件