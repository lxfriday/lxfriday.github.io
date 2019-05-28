---
title: HTTP指南-传输安全
comments: true
top: false
date: 2019-05-26 10:10:37
categories: http 
tags: http
img: https://qiniu1.lxfriday.xyz/WaterM/17a9f745-8a73-436b-ad64-8d40ffc032a4_timg.jpeg
---

## 1 HTTP + 加密 + 认证 + 完整性保护 = HTTPS
HTTPS 并非是应用层的一种新协议。只是 HTTP 通信接口部分用 SSL 和 TLS 协议代替。

通常， HTTP 直接和 TCP 通信。当使用 SSL 时，则演变成先和 SSL 通信，再由 SSL 和 TCP 通信了。即，HTTPS 就是身披 SSL 协议这层外壳的 HTTP。

## 2 HTTPS 的安全通信机制
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/209af313-2982-4d27-83a5-8ed7d3e8f0e0_QQ20190527-160120-https安全通信机制.png" alt="HTTPS 的安全通信机制"><p class="img-desc">HTTPS 的安全通信机制</p></div>

### 2.1 通信过程
1. 客户端通过发送 Client Hello 报文开始 SSL 通信。报文中包含客户端支持的 SSL 的指定版本、加密组件（Cipher suite）列表（所使用的加密算法及秘钥长度）。
1. 服务器可进行 SSL 通信时，会以 Server Hello 报文作为应对答。和客户端一样，在报文中包含 SSL 版本以及加密组件。服务器的加密组件内容是从接收到的客户端加密组件内筛选出来的。
1. 之后服务器发送 Certificate 报文。报文中包含公开密钥证书。
1. 最后服务器发送 Server Hello Done 报文通知客户端，最初阶段的 SSL 握手协商部分结束。
1. SSL 第一次握手结束之后，客户端以 Client Key Exchange 报文作为回应。报文中包含通信加密中使用的一种被称为 Pre-master secret 的随机密码串。该报文已用步骤3中的公开密钥进行加密。
1. 接着客户端继续发送 Change Cipher Spec 报文。该报文会提示服务器，在此报文之后的通信会采用 Pre-master secret 密钥加密。 
1. 客户端发送 Finished 报文。该报文包含连接至今全部报文的整体校验值。这次握手协商是否能够成功，要以服务器是否能够正确解密该报文作为判定标准。
1. 服务器同样发送 Change Cipher Spec 报文。
1. 服务器同样发送 Finished 报文。
1. 服务器和客户端的 Finished 报文交换完毕之后，SSL 连接就算建立完成。当然，通信会受到 SSL 的保护。从此处开始进行应用层协议的通信，及发送 HTTP 请求。
1. 应用层协议通信，及发送 HTTP 响应。
1. 最后由客户端断开连接。断开连接的时，发送 close_notify 报文。上图做了省略，这部之后再发送 TCP FIN 报文来关闭与 TCP 的通信。

在以上的流程中，应用层发送数据时会附加一种叫做 MAC（Message Authentication Code）的报文摘要。MAC能够查知报文是否遭到篡改，从而保护报文的完整性。

### 2.2 SSL 和 TLS
HTTPS 使用 SSL 和 TLS 这两个协议。

SSL 技术最初是由网景先倡导的，开发过 SSL3.0 之前的版本。目前主导权已经转移到 IETF 手中。

IETF 以 SSL 3.0 为基准，后又制定了 TLS1.0、TLS1.1 和 TLS1.2。TLS 是以 SSL 为原型开发的协议，有时会统一称该协议为 SSL。当前主流的版本是 SSL3.0 和 TLS 1.0。

由于 SSL1.0在设计之初被发现出了问题，就没有实际使用。SSL2.0 也被发现存在问题，所以很多浏览器直接废除了该协议版本。

### 2.2 SSL 慢吗
SSL 的慢分两种。一种是指通信慢。另一种是指由于大量消耗 CPU 及内存等资源，导致处理速度变慢。

和使用 HTTP 相比，网络负载可能会变慢 2 到 100 倍。出去和 TCP 连接、发送 HTTP 请求。响应之外，还必须进行 SSL 通信，因此整体上处理通信量不可避免会增加。

另一点是 SSL 必须进行加密处理。在服务器和客户端都需要进行加密和解密的运算处理。因此从结果上讲，比起 HTTP 会更多地消耗服务器和客户端硬件资源，导致负载增强。

针对速度变慢，并没有根本性的解决方案，可以使用 SSL 加速器这种（专用服务器）硬件来改善问题。该硬件为 SSL通信专用硬件，相对软件来讲，能够提高数倍 SSL 的计算速度。仅在 SSL 处理时发挥 SSL 加速器的功效，以分担负载。

## 3 总结

- 明文通信的危害
    被监听、被篡改、被冒充
- HTTPS 加密特点
    每次会话，客户端和服务端会生成一个会话密钥，用密钥加密信息，密钥是对称加密，运算速度快。公钥只加密会话密钥本身，减少加密运算消耗的时间。
- 客户端服务器端建立连接的过程
    - TLS Client => （协议版本、客户端生成的随机数、支持的加密算法、支持的压缩算法）
        - ClientHello
    - TLS Server => （确认使用的协议版本、服务器生成的随机数、确认使用的加密方法、服务器证书）
        - ServerHello
        - ServerCertificate
        - ServerKeyExchange
        - CertificateRequest
        - ServerHelloDone
    - TLS Client => （客户端生成的随机数、编码改变通知、客户端握手结束通知）
        - ClientCertificate
        - ClientKeyExchange
        - CertificateVerify
        - ChangeCipherSpec
        - Finished
    - TLS Server =>（编码改变通知、服务器握手结束通知）
        - ClientCertificate
        - ClientKeyExchange
        - CertificateVerify
        - ChangeCipherSpec
        - Finished

## 4 相关参考
- 图解 HTTP
- [SSL/TLS协议运行机制的概述](http://www.ruanyifeng.com/blog/2014/02/ssl_tls.html)
