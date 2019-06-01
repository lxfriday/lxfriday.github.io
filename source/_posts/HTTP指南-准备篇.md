---
title: HTTP指南-准备篇
comments: true
top: false
date: 2019-05-23 19:28:06
categories: http 
tags: http
img: https://qiniu1.lxfriday.xyz/WaterM/17a9f745-8a73-436b-ad64-8d40ffc032a4_timg.jpeg
---

## 1 TCP/IP 协议族的分层管理
### 1.1 TCP/IP 分层
由应用层、传输层、网络层、链路层构成

#### 1.1.1 应用层
决定了向用户提供服务时通信的活动，HTTP、DNS、FTP都属于这一层

#### 1.1.2 传输层
对上层应用层提供处于网络连接中的两台计算机之间的数据传输。

#### 1.1.3 网络层（网路互连层）
网络层用来处理在网络上流动的数据包。数据包是网络传输的最小数据单位。该层规定了通过怎样的路径到达对方计算机，并把数据包传送给对方。

与对方计算机之间通过多台计算机或网络设备进行传输时，网络层所起的作用就是在众多的选项内选择一条传输路线。

#### 1.1.4 链路层（数据链路层，网络接口层）
用来处理连接网络的硬件部分。包括控制操作系统、硬件的设备驱动、NIC，及光纤等物理可见部分。硬件上的范畴均在链路层的作用范围之内。

### 1.2 TCP 三次握手
用 TCP 协议把数据包送出去之后，TCP 不会对传送后的情况置之不理，它一定会向对方确认是否传送成功。

握手过程中使用了 TCP 的标志（flag）：SYN（synchronize）和 ACK（acknowledgement）。

发送端首先发送一个带 SYN 标志的数据包给对方。接收端受到之后，回传一个带有 SYN/ACK 标志的数据包以示传达确认信息。最后发送端再回传一个带 ACK 标志的数据包，代表"握手"结束。

若在握手过程中某个阶段终端，TCP 协议会再次以相同的顺序发送相同的数据包。

## 2 URI 和 URL
URI 用字符串标识某一互联网资源，而 URL 标识资源的地点（互联网上所处的位置）。URL 是 URI 的子集。

URI 例子：
```html
ftp://ftp.is.co/rfc.txt
http://www.baidu.com/a.txt
mailto:lxfriday@mail.lxfriday.xyz
tel:+1-816-555-1212
telnet://192.168.16:80/
news:comp.info.www.servers.unix
```

URI 格式

`http://user:pass@www.exam.cn:80/dir/index.htm?uid=1#ch1`

- `http://` 协议方案名
- `user:pass` 登录信息（认证）
- `www.exam.cn` 服务器地址
- `80` 服务器端口号
- `/dir/index.htm` 带层次的文件路径
- `uid=1` 查询字符串
- `#ch1` 片段标识符




































