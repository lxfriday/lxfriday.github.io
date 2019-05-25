---
title: HTTP指南-HTTP首部
comments: true
top: false
date: 2019-05-25 00:37:58
categories: http 
tags: http
img: https://qiniu1.lxfriday.xyz/WaterM/17a9f745-8a73-436b-ad64-8d40ffc032a4_timg.jpeg
---

## 1 HTTP 响应报文
在响应中，HTTP 报文由 HTTP 版本、状态码（数字和原因短语）、HTTP 首部字段3部分构成。

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/0b9bdea2-e9a9-4a37-b256-df109ac5f12d_QQ20190525-004849-响应报文的首部信息.png" alt="响应报文的首部信息"><p class="img-desc">响应报文的首部信息</p></div>

## 2 HTTP 首部字段
### 2.1 HTTP 首部字段传递重要信息
使用首部字段是为了给浏览器和服务器提供报文主体大小、所使用的语言、认证信息等内容。

### 2.2 HTTP 首部字段的结构
首部字段名: 字段值

如
- Content-Type: text/html
- Keep-Alive: timeout=15, max=100

### 2.3 4种 HTTP 首部字段类型
- 通用首部字段：请求报文和响应报文都会使用的首部；
- 请求首部字段：从客户端向服务器端发送请求报文时使用的首部。补充了请求的附加内容、客户端信息、响应内容相关优先级等信息；
- 响应首部字段：从服务器端向客户端返回响应报文时使用的首部；
- 实体首部字段：针对请求报文和响应报文的实体部分使用的首部。

#### 2.3.1 HTTP/1.1 首部字段一览
##### 2.3.1.1 通用首部字段
- Cache-Control: 缓存控制
- Connection: 逐跳首部、连接的管理
- Date: 创建报文的日期时间
- Pragma: 报文指令
- Trailer: 报文末端的首部一览
- Transfer-Encoding: 指定报文主体的传输编码方式
- Upgrade: 升级为其他协议
- Via: 代理服务器的相关信息
- Warning: 错误通知

##### 2.3.1.2 请求首部字段
- Accept: 用户代理可处理的媒体类型
- Accept-Charset: 有限的字符集
- Accept-Encoding: 优先的内容编码
- Accept-Language: 优先的语言（自然语言）
- Authorization: Web 认证信息
- Expect: 期待服务器的特定行为
- From: 用户的电子邮件地址
- Host: 请求资源所在服务器
- If-Match: 比较实体标记（ETag）
- If-Modified-Since: 比较资源的更新时间
- If-None-Match: 比较实体标记（与 If-Match 相反）
- If-Range: 资源未更新时发送实体 Byte 的范围请求
- If-Unmodified-Since: 比较资源的更新时间（与 If-Modified-Since 相反）
- Max-Forwards: 最大传输逐跳数
- Proxy-Authorization: 代理服务器要求客户端的认证信息
- Range: 实体的字节范围请求
- Referer: 对请求中 URI 的原始获取方
- TE: 传输编码的优先级
- User-Agent: HTTP 客户端程序的信息

##### 2.3.1.3 响应首部字段
- Accept-Ranges: 是否接受字节范围请求
- Age: 推算资源创建经过的时间
- ETag: 资源的匹配信息
- Location: 零客户端重定向至指定 URI
- Proxy-Authenticate: 代理服务器对客户端的认证信息
- Retry-After: 对再次发起请求的时机要求
- Server: HTTP 服务器的安装信息
- Vary: 代理服务器缓存的管理信息
- WWW-Authenticate: 服务器对客户端的认证信息

##### 2.3.1.4 实体首部字段
- Allow: 资源可支持的 HTTP 方法
- Content-Encoding: 实体主体使用的编码方式
- Content-Language: 实体主体的自然语言
- Content-Length: 实体主体的大小（单位：字节）
- Content-Location: 替代对应资源的 URI
- Content-MD5: 实体主体的报文摘要
- Content-Range: 实体主体的位置范围
- Content-Type: 实体主体的媒体类型
- Expires: 实体主体过期的日期时间
- Last-Modified: 资源最后修改日期时间

##### 2.3.1.5 End-to-end 首部和 hop-by-hop 首部
HTTP 首部字段将定义成功缓存代理和非缓存代理的行为，分成两种类型。

**端到端首部（End-to-end Header）**

分在此类别中的首部会转发给请求/响应对应的最终接收目标，且必须保存在由缓存生成的响应中，另外规定它必须被转发。

**逐跳首部（Hop-to-hop Header）**

分在此类别中的首部只对单次转发有效，会因通过缓存或代理而不再转发。HTTP/1.1 和之后的版本中，如果要使用 hop-to-hop 首部，需提供 Connection 首部字段。

下面列举了 HTTP/1.1 中逐跳首部字段。除了这8个首部字段之外，其他所有字段都属于端到端首部。

- Connection
- Keep-Alive
- Proxy-Authenticate
- Proxy-Authorization
- Trailer
- TE
- Transfer-Encoding
- Upgrade












#### 2.3.2 HTTP/1.1 通用首部字段
##### 2.3.2.1 Cache-Control
通过指定首部字段 Cache-Control 的指令，就能操作缓存的工作机制，其能够控制缓存的行为。

指令的参数是可选的，多个指令之间通过"，"分隔。首部字段 Cache-Control 的指令可用于请求及相应时。

**缓存请求指令**

|指令|参数|说明|
|:-|:-|:-|
|no-cache|无|强制向源服务器再次验证|
|no-store|无|不缓存请求或响应的任何内容|
|max-age=[秒]|必需|响应的最大 Age 值|
|max-stale(=[秒])|可省略|接收已过期的响应|
|min-fresh=[秒]|必需|期望在指定时间内的响应仍有效| 
|no-transform|无|代理不可更改媒体类型|
|only-if-cached|无|从缓存获取资源|
|cache-extension|-|新指令标记（token）|

**缓存响应指令**

|指令|参数|说明|
|:-|:-|:-|
|public|无|可向任一方提供响应的缓存|
|private|可省略|仅向特定用户返回响应|
|no-cache|可省略|缓存前必须先确认其有效性|
|no-store|无|不缓存请求或响应的任何内容|
|no-transform|无|代理不可更改媒体类型|
|must-revalidate|无|可缓存但必须再向源服务器进行确认|
|proxy-revalidate|无|要求中间缓存服务器对缓存的响应有效性再进行确认|
|max-age=秒|必需|响应的最大 Age 值|
|s-maxage=[秒]|必需|公共缓存服务器响应的最大 Age 值|
|cache-extension|-|新指令标记（token）|

表示是否能缓存的指令

**public 指令**：明确表明其他用户也可利用缓存

**private 指令**：响应只以特定的用户作为对象，这与 public 指令的行为相反。

缓存服务器会对该特定用户提供资源缓存的服务，对于其他用户发送过来的请求，代理服务器不会返回缓存。

**no-cache** 指令：放置从缓存中返回过期的资源。

客户端发送的请求中如果包含 no-cache 指令，则表示客户端将不会接收缓存过的相应。于是，中间的缓存服务器必须把客户端情趣发送给源服务器。

如果服务器返回的响应中包含 no-cache 指令，那么缓存服务器不能对资源进行缓存。源服务器以后也将不再对缓存服务器请求中提出的资源有效性进行确认，且禁止其对响应资源进行缓存操作。

**no-store** 指令：不进行缓存。**控制可缓存的对象的指令**

**s-maxage** 指令：**指定缓存期限和认证的指令**

Cache-Control: s-maxage=604800（单位：秒）

s-maxage 指令的功能和 max-age 指令相同，他们的不同点是 maxage 指令只适用于供多位用户使用的公共缓存服务器。也就是说，对于向同一用户反复返回响应的服务器来说，这个指令没有任何作用。

**max-age** 指令：

Cache-Control: max-age=604800（单位：秒）

##### 2.3.2.2 Connection
Connection 首部字段具备如下两个作用。
- 控制不再转发给代理的首部字段
- 管理持久连接

- Connection: 不再转发的首部字段名
    - 在客户端发送请求和服务器返回响应内，使用 Connection 首部字段，可控制不再转发给代理的首部字段。（即 Hop-by-hop 首部）
- 管理持久连接
    - Connection: Close
    - HTTP/1.1 版本的默认连接都是持久连接。为此，客户端会在持久连接上连续发送请求。当服务器端想明确断开连接的时候，则指定 Connection 首部字段的值为 Close。
    - Connection: Keep-Alive
    - HTTP/1.1 之前的版本默认连接都是非持久连接。为此，如果想在旧版本的 HTTP 协议上维持持续连接，则需要指定 Connection 首部字段的值为 Keep-Alive。

##### 2.3.2.3 Date
首部字段 Date 表明创建 HTTP 报文的日期和时间。

HTTP/1.1 协议使用在 RFC1123 中规定的日期时间的格式。

**Date: Tue, 03, Jul 2012 04:40:59 GMT**

##### 2.3.2.4 Pragma
Pragma 是 HTTP/1.1 之前版本的历史遗留字段，仅作为与 HTTP/1.0 的向后兼容而定义。

规范定义的形式唯一，如下。

**Pragma: no-cache**

该首部字段属于通用首部字段，但只用在客户端发送的请求中。客户端会要求所有的中间服务器不返回缓存的资源。

##### 2.3.2.5 Trailer
首部字段 Trailer 会实现说明在报文主体后记录了哪些首部字段。该首部字段可应用在 HTTP/1.1 版本分块传输编码时。

##### 2.3.2.6 Upgrade
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/cd81c4e6-1f2f-49e8-8871-8e9014f10d26_QQ20190525-032535-upgrade.png" alt="upgrade"><p class="img-desc">upgrade</p></div>

首部字段 Upgrade 用于检测 HTTP 协议及其他协议是否可使用更高的版本进行通信，其参数值可以用来指定两个完全不同的通信协议。

##### 2.3.2.7 Via
使用 Via 是为了追踪客户端与服务器之间的请求和响应报文的传输路径。

报文经过代理或网关时，会现在首部字段 Via 中附加该服务器的信息，然后再进行转发。

Via 首部是为了追踪传输路径，所以经常会和 TRACE 方法一起使用。代理服务器接收到由 TRACE 方法发送过来的请求（Max-Forwards: 0）时，代理服务器就不能再转发该请求了。这种情况下，代理服务器会将自身的信息附加到 Via 首部后，返回该请求的相应。

##### 2.3.2.7 Warning
HTTP/1.1 的 Warning 首部是从 HTTP/1.0 的相应首部（Retry-After）演变过来的。该首部通常会告知用户一些与缓存相关的问题的警告。

警告的格式如下

Warning: [警告码][警告的主机:端口号][警告内容][日期时间]

#### 2.3.3 请求首部字段
请求首部字段是从客户端往服务器端发送请求报文中所使用的字段，用于补充请求的附加信息、客户端信息、对响应内容相关的优先级等内容。

##### 2.3.3.1 Accept
Accept 首部字段可通知服务器，用户代理能够处理的媒体类型及媒体类型的相对优先级。可使用 type/subtype 这种形式，一次指定多种媒体类型。

Accept: text/html,application/xhtml+xml,application/xml;q=0

例如：

- 文本文件
    text/html, text/plain, text/css ...
    application/xhtml+xml, application/xml ...
- 图片文件
    image/jpeg, image/gif, image/png ...
- 视频文件
    video/mpeg, video/quicktime ...
- 应用程序使用的二进制文件
    application/octet-stream, application/zip ...

当服务器提供多种内容时，将会首先返回权重值最高的媒体类型。

若想要给显示的媒体类型增加优先级，则使用 q= 来额外表示权重值，用分号（；）进行分割。权重值 q 的范围是 0~1（可精确到小数点后3位），且1为最大值。不指定权重 q 值时，默认权重为 1=1.0

##### 2.3.3.2 Accept-Charset
用来通知服务器用户dialing支持的字符集及字符集的相对优先顺序。另外，可一次性指定多种字符集。q 表示相对优先级。

Accept-Charset: ios-8859-5, unicode-1-1;q=0.8

##### 2.3.3.3 Accept-Encoding
用来告知服务器用户代理支持的内容编码及内容编码的优先级顺序。可一次性指定多种内容编码。

Accept-Encoding: gzip,deflate

列举几种编码：

- gzip
    由文件压缩程序 zip（GNU zip） 生成的编码格式（RFC1952），采用 Lempel-Ziv 算法（LZ77）及32位循环冗余校验 CRC。
- compress
    由 UNIX 文件压缩程序 compress 生成的编码格式，采用 Lempel-Ziv-Welch 算法（LZW）。
- deflate
    组合使用 zlib 格式（RFC1950）及由 deflate 压缩算法（RFC1952）生成的编码格式。
- identity
    不执行压缩或不会变化的默认编码格式

##### 2.3.3.4 Accept-Language
用来告知服务器用户代理能够处理的自然语言集（中文或英文等），以及自然语言集的相对优先级。

Accept-Language: zh-cn,zh;q=0.7,en-us,en;q=0.3

上述首部字段，客户端在服务器有中文资源的情况下，会请求其返回中文版对应的响应，没有中文版时，则请求返回英文版响应。

##### 2.3.3.5 Authorization
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/a2427a9f-1940-4599-b443-399a1fbbcd6a_QQ20190525-164612-authorization.png" alt="Authorization"><p class="img-desc">Authorization</p></div>

Authorization: Basic dWdasdsfkmsdlkfnlsdknflsdjnf==

首部字段 Authorization 是用来告知服务器，用户代理的认证信息（证书值）。通常，想要通过服务器认证的用户代理会在接收到返回的 401 状态码响应之后，把首部字段 Authorization 加入到请求中。

##### 2.3.3.5 Expect
Expect: 100-continue

告知服务器期望出现的某种特定行为。

##### 2.3.3.6 From
用来告知服务器使用用户代理的用户的电子邮件地址。七亩地是为了显示搜索引擎等用户代理的负责人的电子邮件联系方式。使用代理时，应尽可能包含 From 字段。

From； info@lxfriday.xyz

##### 2.3.3.7 Host
告知服务器，请求的资源所处的互联网主机名和端口号。Host 首部字段再 HTTP/1.1 规范内是唯一一个必须被包含在请求内的首部字段。

Host： www.lxfriday.xyz

首部字段 Host 和以单台服务器分配多个域名的虚拟主机的工作机制有很密切的关联，这是首部字段 Host 必须存在的意义。

请求被发送至服务器时，请求中的主机名会用 IP 地址直接替换解决。但如果相同的 IP 地址下部署运行着多个域名，那么服务器就会无法理解究竟是哪个域名对应的请求。因此就需要使用 Host 来明确指出请求的主机名。若服务器未设定主机名，那直接发送一个空值即可。

Host: 

##### 2.3.3.8 If-Match
形如 If-xxx 这种样式的请求首部字段，都可称为条件请求。服务器接收到附带条件的请求后，只有判断指定条件为真时，才会执行请求。

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/f2b5275b-defc-4e86-94f8-3ed57666e188_QQ20190525-170218-if-match.png" alt="if-match"><p class="img-desc">if-match</p></div>

图中，只有当 If-Match 的字段值和 ETag 值匹配时，服务器才会接受请求。

If-Match: "123456"

首部字段 If-Match，属附带条件之一，他会告知服务器匹配资源所用的实体标记值（ETag）。这时的服务器无法使用弱 ETag 值。

**服务器会比对 If-Match 和资源的 ETag 值，仅当两者一致时，才会执行请求。反之，则返回状态码 412 Precondition Failed 的响应**

**还可以使用星号（*）指定 If-Match 的字段值。针对这种情况，服务器将会忽略 ETag 的值，只要资源存在就处理请求。**

##### 2.3.3.9 If-Modified-Since
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/8702c368-27bd-44d1-9a22-6dc315c51c85_QQ20190525-170745-if-modified-since.png" alt="if-modified-since"><p class="img-desc">If-Modified-Since</p></div>

If-Modified-Since: Thu, 15 Apr 2004 00:00:00 GMT

首部字段，属于附带条件之一，他会告知服务器若 If-Modified-Since 字段早于资源的更新时间，则希望能处理该请求。而在指定 If-Modified-Since 字段值的日期时间之后，如果请求的资源没有更新过，则返回状态码 304 Not Modified 的响应。

##### 2.3.3.9 If-None-Match
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/989ad603-aaee-4f3f-9d93-287429eeba3d_QQ20190525-171356-If-None-Match.png" alt="If-None-Match"><p class="img-desc">If-None-Match</p></div>

只有在 If-None-Match 的字段值与 ETag 值不一致时，可处理该请求。

##### 2.3.3.10 If-Range
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/fff385b0-3218-4b46-ac2e-6ec15d164b10_QQ20190525-172807-If-Range.png" alt="If-Range"><p class="img-desc">If-Range</p></div>

首部字段 If-Range 属于附带条件之一。它告知服务器若指定的 If-Range 字段值（ETag值或者时间）和请求资源的 ETag 值或时间相一致时，则作为范围请求处理。反之，则返回全部资源。

##### 2.3.3.11 If-Unmodified-Since
If-Unmodified-Since: Thu, 03 Jul 2012 00:00:00 GMT

首部字段 If-Unmodified-Since 和首部字段 If-Modified-Since 的作用相反。他的作用是告知服务器，指定的请求资源只有在字段值内指定的日期时间之后，未发生更新的情况下， 才能处理请求。

如果在指定日期时间后繁盛了更新，则以 412 Precondition Failed 作为响应返回。

##### 2.3.3.12 Max-Forwards
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/9a6089cb-b583-42bd-83a9-83bbb7a0a435_QQ20190525-173649-Max-Forwards.png" alt="Max-Forwards"><p class="img-desc">Max-Forwards</p></div>

每次转发数值减1，当和数值变0时返回响应。

Max-Forwards: 10

通过 TRACE 或者 OPTIONS 方法，发送包含首部字段 Max-Forwards 的请求时，该字段以十进制整数形式指定可经过的服务器最大数目。**服务器在往下一个服务器转发请求之前，Max-Forwards 的值减少1后重新赋值。当服务器接收到 Max-Forwards 值为0的请求时，则不再进行转发，而是直接返回响应**

##### 2.3.3.13 Proxy-Authorization
Proxy-Authorization: Basic damklsfnlksdfnlsd

接收到从代理服务器发来的认证质询时，客户端会发哦少年宫包含首部字段的 Proxy-Authorization 的请求，以告知服务器认证所需要的信息。

这个行为是与客户端和服务器之间的 HTTP 访问认证相类似的，不同之处在于，认证行为发生在客户端与代理之间。客户端与服务器之间的认证，使用 Authorization 可起到相同作用。

##### 2.3.3.14 Range
Range: bytes=5001-10000

对于只需获取部分资源的范围请求，包含首部字段 Range 即可告知服务器资源的指定范围。上面的示例表示请求获取第5001字节至10000字节的资源。

接收到附带 Range 首部字段请求的服务器，**会在处理请求之后返回状态码为 206 Partial Content 的响应。无法处理该范围请求时，则会返回状态 200 OK的响应及全部资源。**

##### 2.3.3.15 Referer
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/58a273e2-eeec-41c7-b11b-7284541d9c1c_QQ20190525-201402-referer.png" alt="Referer"><p class="img-desc">Referer</p></div>

首部字段 Referer 会告知服务器请求的原始资源的 URI。

客户端一般都会发送 Referer 首部字段给服务器。但当直接在服务器地址栏输入 URI，或处于安全性的考虑，也可以不发送该首部字段。

注意，Referer 的正确拼写应该是 **Referrer**

##### 2.3.3.16 User-Agent
用于传达客户端的种类

User-Agent 会将创建请求的客户端信息传达给服务器。

##### 2.3.4 响应首部字段
响应首部字段是由服务器端向客户端返回响应报文中所使用的字段，用于补充响应的附加信息、服务器信息，以及对客户端的附加要求等信息。

##### 2.3.4.1 Accept-Ranges
- Accept-Ranges: none
    不能处理范围请求
- Accept-Ranges: bytes
    能处理范围请求时

##### 2.3.4.2 Age
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/a134e8d8-1fd7-4082-9ee0-d7972b6d943c_QQ20190525-202227-age.png" alt="Age"><p class="img-desc">Age</p></div>

告知客户端，源服务器在多久前创建了响应。字段单位为秒。

若创建该响应的服务器是缓存服务器，Age 值是指缓存后的响应再次发起认证到认证完成的时间值。代理创建响应时必须加上首部字段 Age。

##### 2.3.4.3 ETag
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/c7608fe4-7328-46cc-96eb-a3c1acca1ce0_QQ20190525-202640-Etag.png" alt="ETag"><p class="img-desc">ETag</p></div>

ETag 能告知客户端实体标识。它是一种可将资源以字符串形式做唯一性标识的方式。服务器会为每份资源分配对应的 ETag 值。

另外，当资源更新时，ETag 值也需要更新。生成 ETag 值时，并没有统一的算法规则，而仅仅是由服务器来分配。

**强 ETag 值**

无论实体发生多么细微的变化都会改变其值。

ETag: "china-1234"

**弱 ETag 值**

弱 ETag 值只用于提示资源是否相同。只有资源发生了根本改变，产生差异的时候才会改变 ETag 值。这时会在字段最开始处附加 W/。

ETag: W/"china-1234""

##### 2.3.4.4 Location
可将响应结束方引导到某个与请求 URI 位置不同的资源。

该字段基本上会配合 3XX: Redirection 的响应，提供重定向的 URI。

几乎所有浏览器在接收到包含 Location 的响应之后，都会强制性地尝试对已提示的重定向资源的访问。

##### 2.3.4.5 Proxy-Authenticate
Proxy-Authenticate: Basic realm="Usagidesign Auth""

Proxy-Authenticate 会把由代理服务器所要求的认证信息发送给客户端。

##### 2.3.4.6 Retry-After
Retry-After: 120

Retry-After 告知客户端应该在多久之后再次发送请求。主要配合状态码 503 Service Unavailable 响应，或 3XX Redirect 响应一起使用。

字段值可以指定为具体的日期时间（Wed，04 Julia2012 06:22:33 GMT 等格式），也可以是创建响应之后的秒数。

##### 2.3.4.7 Server
Server: Apache/2.2.11 (Unix)

告知客户端当前服务器上安装的 HTTP 服务器应用程序的信息。不单单会标出服务器上的软件应用名称，还有可能包括版本号和安装时启用的可选项。

Server: Apache/2.2.2 (Unix) PHP/5.2.6

##### 2.3.4.8 Vary
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/40ed79bd-40eb-4b2f-a15e-4487b322a734_QQ20190525-210003-Vary.png" alt="Vary"><p class="img-desc">Vary</p></div>

当代理服务器接收到带有 Vary 首部字段指定获取资源的请求时，如果使用的 Accept-Language 字段的值相同，那么久直接从缓存返回响应，反之则需要从源服务器端获取资源之后才嫩恶搞作为资源返回。

Vary: Accept-Language

Vary 可对缓存进行控制。源服务器会向代理服务器传达关于本地缓存使用方法的命令。

从代理服务器接收到源服务器返回包含 Vary 指令项的响应之后，若再要进行缓存，仅对请求中包含有相同 Vary 指定首部字段的请求返回缓存。即使对相同资源发起请求，但由于 Vary 指定的首部字段不相同，因此必须要从源服务器重新获取资源。

##### 2.3.4.9 WWW-Authenticate
WWW-Authenticate 用于 HTTP 访问认证。他会告知客户端适用于访问请求 URI 所指定资源的认证方案（Basic 或是 Digest）和带参数提示的质询。状态码 401 Unauthorized 响应中，肯定带有 WWW-Authenticate。

##### 2.3.5 实体首部字段
实体首部字段是包含在请求报文和响应报文中的实体部分所使用的首部，用于补充内容的更新时间等与实体相关的信息。

##### 2.3.5.1 Allow
Allow: GET, HEAD

首部字段 Allow 用于通知客户端能够支持 Request-URI 指定资源的所有 HTTP 方法。当服务器接收到不支持的 HTTP 方法时，会有状态码 405 Method Not Allowed 作为响应返回。与此同时，还会把所有能支持的 HTTP 方法写入首部字段 Allow 后返回。

##### 2.3.5.2 Content-Encoding
Content-Encoding 会告知客户端服务器对实体的主体部分选用的内容编码方式。内容编码是指在不丢失实体信息的前提下所进行的压缩。

主要采用以下四种内容编码方式：
- gzip
- compress
- deflate
- identity

##### 2.3.5.3 Content-Language
告知客户端实体主体使用的自然语言（中文或者英文）

Content-Language： zh-CN

##### 2.3.5.4 Content-Length
Content-Length: 15000

首部字段 Content-Length 表明了实体主体部分的大小（单位是字节）。对实体主体进行内容编码传输时，不能再使用 Content-Length。 

##### 2.3.5.5 Content-Location
给出与报文主体部分相对应的 URI。和首部字段 Location 不同，Content-Location 表示的报文主体返回资源对应的 URI。

如，对于使用首部字段 Accept-Language的服务器驱动型请求，当返回的页面内容与实际请求的对象不同时，首部字段 Content-Location 内会写明 URI。（访问 https://lxfriday.xyz 返回的是 https://lxfriday.xyz/index.html 等类似的情况）

##### 2.3.5.6 Content-MD5
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/cb35117b-3758-414a-9f0d-27c8d4cf0433_QQ20190525-212458-Content-MD5.png" alt="Content-MD5"><p class="img-desc">Content-MD5</p></div>

客户端会对接收的保温主体执行相同的 MD5 算法，然后与首部字段 Content-MD5 的字段值比较。

Content-MD5: ADJKNKJFSNkjfbskdJBSKHSLJFLbjksfbksdsdty==

首部字段 Content-MD5 是一串由 MD5 算法生成的值，其目的在于检查报文主体在传输过程中是否保持完整，以及确认传输到达。

对报文主体执行 MD5 算法获得的 128 位二进制数，再通过 Base64 编码后将结果写入 Content-MD5 字段值。由于 HTTP 首部无法记录二进制值，所以也要通过 Base64 编码处理。
 
##### 2.3.5.7 Content-Range
<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/b6a8b0cb-5305-4996-938a-7c67654bab99_QQ20190525-213002-Content-Range.png" alt="Content-Range"><p class="img-desc">Content-Range</p></div>

其能告知客户端作为响应返回的实体的那个部分符合范围请求。字段值以字节为单位，表示当前发送部分及整个实体大小。

##### 2.3.5.8 Content-Type
Content-Type: text/html; Charset=UTF-8

其说明实体内对象的媒体类型。和首部字段 Accept 一样，字段用 type/subtype 形式赋值。

##### 2.3.5.9 Expires
Expires: Wed, 04 Jul 2012 08:26:05 GMT

Expires 会将资源失效的日期告知客户端。缓存服务器在接收到含有首部字段 Expires 的响应之后，会以缓存来应答请求，在 Expires 字段指定的时间之前，相应的副本会一直被保存。当超过指定的时间之后，缓存服务器在请求发送过来时，会转向源服务器请求资源。

源服务器不希望缓存服务器对资源缓存时，最好在 Expires 字段内写入与首部字段 Date 相同的时间值。

**当 Cache-Control 有指定 max-age 时，比起 Expires，会优先处理 max-age 指令**

##### 2.3.5.10 Last-Modified
其指定资源最终修改的时间。一般这个值就是 request-URI 指定资源被修改的时间。

##### 2.3.6 和 Cookie 相关的首部字段
##### 2.3.6.1 Set-Cookie
Set-Cookie: status=enable; expires=Tue, 05 Jul 2011 07:26:31; secure; HttpOnly

|属性|说明|
|:-|:-|
|NAME=VALUE|赋予名称和值|
|expires=DATE|有效期（若不明确指定，则默认为关闭浏览器之前为止）|
|path=PATH|将服务器上的路径作为 Cookie 的使用对象（若不指定则默认为文档所在的文件目录）|
|domain=域名|作为 Cookie 适用对象的域名（不指定则默认为创建 Cookie 的服务器的域名）|
|Secure|仅在 HTTPS 安全通信时才会发送 Cookie|
|HttpOnly|加以限制，使 Cookie 不能被 JS 脚本访问|

##### 2.3.6.2 Cookie
Cookie: status=enable

告知服务器，当客户端想获得 HTTP 状态管理支持时，就会在请求中包含从服务器接收到的 Cookie。

#### 2.3.7 其他首部字段
HTTP 首部字段是可以自行扩展的。

##### 2.3.7.1 X-Frame-Options
其属于 HTTP 响应首部，用于控制网站内容在其他 Web 网站的 Frame 标签内的显示问题。主要目的是为了防止点击劫持（clickjacking）攻击。

有两个可指定的字段值：
- DENY: 拒绝
- SAMEORIGIN: 仅铜源峡的页面

##### 2.3.7.2 X-XSS-Protection
其属于 HTTP 响应首部，它是针对跨站脚本攻击（XSS）的一种对策，用于控制浏览器 XSS 防护机制的开关。

- 0: 将 XSS 过滤设置成无效状态
- 1: 将 XSS 过滤设置成有效状态

##### 2.3.7.3 DNT
DNT: 1

其属于 HTTP 请求首部，其中 DNT 是 Do Not Track 的简称，意为拒绝个人信息被收集；是表示拒绝被精准广告追踪的一种方法。

- 0: 统一被追踪
- 1: 拒绝被追踪
