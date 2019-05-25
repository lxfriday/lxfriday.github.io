---
title: HTTP指南-简单HTTP协议
comments: true
top: false
date: 2019-05-23 21:22:00
categories: http 
tags: http
img: https://qiniu1.lxfriday.xyz/WaterM/17a9f745-8a73-436b-ad64-8d40ffc032a4_timg.jpeg
---

## 1 HTTP 方法
GET、POST、PUT、HEAD、DELETE、OPTIONS、CONNECT、TRACE

- GET：获取资源
- POST：传输实体主体
- PUT：传输文件
- HEAD：获得报文首部
- DELETE：删除文件
- OPTIONS：询问支持的方法
- TRACE：追踪路径
- CONNECT：要求用隧道协议连接代理
    - CONNECT 方法要求在与代理服务器通信时建立隧道，实现用隧道协议进行 TCP 通信。
    - 主要使用 SSL（Secure Sockets Layer，安全套接层）和 TLS（Transport Layer Security，传输层安全）协议把通信内容加密后经网络隧道传输。
