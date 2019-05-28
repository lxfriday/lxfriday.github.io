---
title: HTTP指南-准备篇
comments: true
top: false
date: 2019-05-23 19:28:06
categories: http 
tags: http
img: https://qiniu1.lxfriday.xyz/WaterM/17a9f745-8a73-436b-ad64-8d40ffc032a4_timg.jpeg
---

## 1 英文字母解释
1. HTTP (HyperText Transfer Protocol) 超文本传输协议
1. URL (Uniform Resource Locator) 统一资源定位符
1. FTP (File Transfer Protocol) 文件传输协议
1. DNS (Domain Name Service) 域名服务
1. TCP (Transmission Control Protocol) 传输控制协议
1. UDP (User Data Protocol) 用户数据报协议
1. NIC (Network Interface Card) 网络适配器（网卡）
1. IP (Internet Protocol) 网络协议
1. MAC (Media Access Control Address) 媒体访问控制地址
1. ARP (Address Resolution Protocol) 地址解析协议
1. RFC (Request for Comments) 征求修正意见书
1. REST (Representational State Transfer) 表征状态转移
1. TLS (Transport Layer Security) 传输层安全
1. SSL (Secure Socket Layer) 安全套接层
1. MIME (Multipurpose Internet Mail Extensions) 多用途因特网邮件扩展
1. WebDAV (Web-based Distributed Authoring and Versioning) 基于万维网的分布式创作和版本控制
1. CRC (Cyclic Redundancy Check) 循环冗余校验
1. Dos 攻击 (Denial of Service) 拒绝服务攻击
1. MITM (Man-in-the-Middle) 中间人攻击，请求或响应在传输图中，遭攻击者拦截并篡改内容的攻击成为中间人攻击
1. EV SSL 整数(Extended Validation SSL Certificate) 
1. CRL (Certificate Revocation List) 整数吊销机制
1. IETF (Internet Engineering Task Force) 互联网工程任务组
1. XSS (Cross-Site Scripting) 跨站脚本攻击
1. CSRF (Cross-site Request Forgery) 跨站请求伪造攻击


## 2 TCP/IP 协议族的分层管理
### 2.1 TCP/IP 分层
由应用层、传输层、网络层、链路层构成

#### 2.1.1 应用层
决定了向用户提供服务时通信的活动，HTTP、DNS、FTP都属于这一层

#### 2.1.2 传输层
对上层应用层提供处于网络连接中的两台计算机之间的数据传输。

#### 2.1.3 网络层（网路互连层）
网络层用来处理在网络上流动的数据包。数据包是网络传输的最小数据单位。该层规定了通过怎样的路径到达对方计算机，并把数据包传送给对方。

与对方计算机之间通过多台计算机或网络设备进行传输时，网络层所起的作用就是在众多的选项内选择一条传输路线。

#### 2.1.4 链路层（数据链路层，网络接口层）
用来处理连接网络的硬件部分。包括控制操作系统、硬件的设备驱动、NIC，及光纤等物理可见部分。硬件上的范畴均在链路层的作用范围之内。

### TCP 三次握手
用 TCP 协议把数据包送出去之后，TCP 不会对传送后的情况置之不理，它一定会向对方确认是否传送成功。

握手过程中使用了 TCP 的标志（flag）：SYN（synchronize）和 ACK（acknowledgement）。

发送端首先发送一个带 SYN 标志的数据包给对方。接收端受到之后，回传一个带有 SYN/ACK 标志的数据包以示传达确认信息。最后发送端再回传一个带 ACK 标志的数据包，代表"握手"结束。

若在握手过程中某个阶段终端，TCP 协议会再次以相同的顺序发送相同的数据包。

## 3 URI 和 URL
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
### URI 格式
`http://user:pass@www.exam.cn:80/dir/index.htm?uid=1#ch1`

- `http://` 协议方案名
- `user:pass` 登录信息（认证）
- `www.exam.cn` 服务器地址
- `80` 服务器端口号
- `/dir/index.htm` 带层次的文件路径
- `uid=1` 查询字符串
- `#ch1` 片段标识符

### 压缩传输的内容编码
使实体内容变小之后发送

常用的内容编码有：
- gzip（GNU zip）
- compress（UNIX 系统的标准压缩）
- deflate（zlib）
- identity（不进行编码）




































