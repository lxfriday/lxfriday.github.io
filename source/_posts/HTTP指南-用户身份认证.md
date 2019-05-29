---
title: HTTP指南-用户身份认证
comments: true
top: false
date: 2019-05-28 14:36:38
categories: http 
tags: http
img: https://qiniu1.lxfriday.xyz/WaterM/17a9f745-8a73-436b-ad64-8d40ffc032a4_timg.jpeg
---

## 1 HTTP/1.1 认证方式分类
- BASIC 认证（基本认证）
- DIGEST 认证（摘要认证）
- SSL 客户端认证
- FormBase 认证（基于表单认证）

### 1.1 BASIC 认证（基本认证）
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/13142fd9-9bb6-4a71-8e23-b88000ba68b4_WX20190528-144136-basic认证.png" alt="BASIC 认证"><p class="img-desc">BASIC 认证</p></div>

它是 Web 服务器与通信客户端之间进行的认证方式。

1. 当请求的资源需要 BASIC 认证时，服务器会随状态码 401 Authorization Required，返回带 WWW-Authenticate 首部字段的响应。该资源内包含认证的方式（BASIC）及 Request-URI 安全域字符串（realm）。
1. 接收到状态码401的客户端为了通过 BASIC 认证，需要将用户 ID 及密码发送给服务器。发送的字符串内容是由 ID 和密码构成，两者中间以冒号（:）连接后，再经过 Base64 编码处理。
    假设用户 ID 为 guest，密码是 guest，连接起来就会形成 guest:guest 这样的字符串，经过 Base64 编码处理，最后的结果即 Z3Vlc3Q6Z3Vlc3Q=。把这串字符串写入首部字段 Authorization 后，发送请求。
    当用户代理为浏览器时，用户仅需输入用户 ID 和密码即可，之后，浏览器会自动完成到 Base64 编码的转换工作。
1. 接收到包含 Authorization 请求的服务器，会对认证信息的正确性进行验证。若通过验证，则返回一条包含 Request-URI 资源的响应。

Basic 认证使用的 Base64 编码不属于加密方式，在明文解码之后就是用户的 ID 和密码，在 HTTP 等非加密通信的线路上进行 BASIC 认证的过程中，如果被窃听，身份信息被被盗的风险很高。

### 1.2 DIGEST 认证
DIGEST 认证使用执行/响应的方式，但不会像 BASIC 认证那样直接发送明文密码。

质询响应是指，一开始一方会先发送认证要求给另一方，接着使用从另一方那接收到的质询码计算生成响应码。最后将响应码返回给对方进行认证。 

### 1.3 SSL 客户端认证
SSL 客户端认证是借由 HTTPS的客户端证书完成认证的方式。凭借客户端证书认证，服务器可确认访问是否来自已经登录的客户端。

### 1.4 表单认证
表单认证即应用设计者在页面上设计的表单，工用户输入用户的身份信息，进行登录操作。




























