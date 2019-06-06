---
title: 看了那么多，TCPIP究竟是什么（二）
comments: true
top: false
date: 2019-06-06 20:31:23
categories: tcp
tags: tcp
img: https://qiniu1.lxfriday.xyz/WaterM/b99c7b46-c8cb-4737-80ae-a3e1c5d1f646_WX20190531-142955-tcp.png
---

> 本文约 1200 字，读完需要约 4分钟
> “本文主要讲解TCP如何实现可靠传输。”


TCP 的传输过程有点像送快递，快递用顺丰送的很快，用其他的可能会拖延几天，送的路上可能会不小心弄丢了，快递送到之后，对方需要签收以确保自己完整拿到了快递。


TCP 传输的时候也可能会出现丢失数据、重复发送或者发送顺序混乱的问题。


下面是 TCP 可靠传输的基础。


## 问题1 TCP 分段传输的段长度怎么确定？


在建立连接的时候就会确定传输的段长度（也称作最大消息长度，MSS），之后的传输都是以这个 MSS 作为传输单位。


确定的过程大概是这样：


主机1：大哥，咱们的MSS用4500怎么样？（第一次握手）


主机2：小老弟，传这么多我吃不消啊，就传1400吧，少量多次呀！（第二次握手）


主机1：好的，大哥，之后咱们都以1400作为传输单位啦！（第三次握手）


实质是在两者之间选一个较小的作为传输单位。


## 问题2 TCP 传输数据是分段的，如何保证数据被完整的接收到？


通过序列号与确认号提高可靠性。


发送者发送数据的时候带有数据的序列号（seq）和数据长度，而接受者接收到这段数据就会返回确认号（ack），发送者收到 ack 就知道这段数据已经被成功接收到了。


<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/56b755ee-d4f3-4398-886d-da5ea79558ab_TCP详解1.png" alt="TCP 可靠传输"><p class="img-desc">TCP 可靠传输</p></div>


## 问题3 数据发送的时候可能会丢失，如何重发呢？


TCP 被要求能在各种环境下提供高性能通信，即使网络拥堵（类似堵车）也要能保证这点。它在每次传输时都会计算往返的时间差，数据重发之后若收不到应答还会继续发送，等待确认的时间会以2倍、4倍的方式成指数增长。但是发送方也不会一直重发，达到固定次数之后，会认为连接发生了异常，强制地断开连接。


<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/fdbbb592-20ea-43dd-ad5d-b0d5f4c61f5f_TCP详解2.png" alt="多次发送数据没有回复，一段时间之后断开连接"><p class="img-desc">多次发送数据没有回复，一段时间之后断开连接</p></div>

## 问题4 确认对方接收到前一段之后才能发送下一段，如何提升发送速度呢？

TCP 使用窗口控制机制。 


简单来讲就是在一个大范围内分多次发送，先不管对方有没有接收到，等这个范围发送完了之后，再看对方没收到哪段就发送哪段。


这里，发送端有一个内存池，接收到对应的确认号之后会从这个池子清除掉，发送端收到三次相同的确认时（确认号相同），就会将对方需要的数据发送出去。因为发送多次确认，就表示这一段对应的数据对方没有接收到。


MSS：Maximum Segment Size 最大消息长度


之所以会专门讲 TCP，是因为 TCP 的应用非常广泛，很多通信都会用到 TCP ，一些过程的交互也会设计成类似 TCP 三次握手的模式。另外面试时，考查协议问到 TCP 的概率也极其高。

<section class="" data-style-type="5" data-tools="新媒体排版" data-id="2177544"><p style="max-width: 100%;box-sizing: border-box;min-height: 1em;text-align: center;word-wrap: break-word !important;" class=""><span data-txtless="spin" data-txtlessp="120" style="font-size: 14px;max-width: 100%;color: rgb(228, 157, 172);box-sizing: border-box !important;word-wrap: break-word !important;">◆&nbsp;</span><span style="font-size: 14px;"></span><span style="font-size: 14px;max-width: 100%;color: rgb(250, 211, 172);box-sizing: border-box !important;word-wrap: break-word !important;">◆&nbsp;</span><span style="font-size: 14px;"></span><span data-txtless="spin" data-txtlessp="-120" style="font-size: 14px;max-width: 100%;color: rgb(255, 233, 234);box-sizing: border-box !important;word-wrap: break-word !important;">◆ &nbsp;<span data-txtless="spin" data-txtlessp="120" style="max-width: 100%;color: rgb(121, 134, 175);box-sizing: border-box !important;word-wrap: break-word !important;">◆&nbsp;</span><span style="max-width: 100%;color: rgb(17, 163, 215);box-sizing: border-box !important;word-wrap: break-word !important;">◆</span></span></p></section>

<br />

<section class="" data-style-type="5" data-tools="新媒体排版" data-id="2170287"><section style="float: left;margin:0 15px 10px;color: inherit;"><p style="color: inherit;line-height:72px;"><span style="color:rgb(95, 156, 239);font-size:72px;">06</span></p><p style="color: inherit;line-height:72px;"><span style="color: inherit;font-size: 24px;font-family: -apple-system-font, BlinkMacSystemFont, &quot;Helvetica Neue&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei UI&quot;, &quot;Microsoft YaHei&quot;, Arial, sans-serif;">06,2019</span></p></section><section style="margin: 24px 5px -20px -8px;padding: 0px;border-right-width: 10px;border-left-width: 0px;border-right-style: solid;border-right-color: rgb(95, 156, 239);border-left-color: rgb(95, 156, 239);display: inline-block;max-width: 100%;height: 9px;width: 9px;vertical-align: top;float: left;border-bottom-width: 10px !important;border-top-style: solid !important;border-bottom-style: solid !important;border-top-color: transparent !important;border-bottom-color: transparent !important;word-wrap: break-word !important;box-sizing: border-box !important;"></section><section class="" style="margin-left: 125px;line-height: 1.75em;color: rgb(255, 255, 255);padding: 15px;border-radius: 5px;background: rgb(95, 156, 239);"><span style="font-size: 14px;">今天的大新闻是林志玲姐姐宣布结婚了，恭喜她，志玲姐姐一直都是我心目中的女神，突然觉得好温暖。&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: rgb(255, 255, 255);font-size: 14px;background-color: rgb(95, 156, 239);">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;</span><span style="color: rgb(255, 255, 255);font-size: 14px;background-color: rgb(95, 156, 239);">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;</span><span style="color: rgb(255, 255, 255);font-size: 14px;background-color: rgb(95, 156, 239);">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;</span><span style="color: rgb(255, 255, 255);font-size: 14px;background-color: rgb(95, 156, 239);">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;</span><span style="color: rgb(255, 255, 255);font-size: 14px;background-color: rgb(95, 156, 239);">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;</span><span style="color: rgb(255, 255, 255);font-size: 14px;background-color: rgb(95, 156, 239);">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; </span>另外 5G 牌照今天下发，我觉得是标志性的一刻，中国移动、<span style="color: rgb(255, 255, 255);font-size: 14px;background-color: rgb(95, 156, 239);">中国</span>联通、<span style="color: rgb(255, 255, 255);font-size: 14px;background-color: rgb(95, 156, 239);">中国</span>电信、<span style="color: rgb(255, 255, 255);font-size: 14px;background-color: rgb(95, 156, 239);">中国</span>广电四家获得牌照，5G 离我们的生活越来越近了。我记得很小的时候我还羡慕别人用手机上 QQ 聊天、用 QQ 音乐听歌，现在这已经成了基本的生活习惯，不得不让人憧憬未来5-10年，网络技术会如何影响我们的日常生活。</span></section></section>

<br />

<section class="" data-style-type="1" data-tools="新媒体排版" data-id="12893"><section class="" style="margin-top:20px;"><section style="margin: 20px 5px 5px;padding: 5px;border-width: 2px;border-style: solid;border-color: rgb(240, 84, 84);border-radius: 4px;"><section style="display:flex;justify-content: center;"><section style="margin-top: -20px;padding: 0 25px;background-color:rgb(254, 254, 254);"><p class="" style="color:#f05454;font-size:18px;min-width:1em;"><span style="font-family: -apple-system-font, BlinkMacSystemFont, &quot;Helvetica Neue&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft YaHei UI&quot;, &quot;Microsoft YaHei&quot;, Arial, sans-serif;">推荐阅读</span></p></section></section><section style="padding: 20px;"><p class="" style="white-space: normal;color: rgb(85, 85, 85);font-size: 14px;"><a href="http://mp.weixin.qq.com/s?__biz=MzU3MzcxMzg2Mw==&amp;mid=2247483697&amp;idx=1&amp;sn=a35ff697f4b57f6d42d58347e51f3ea7&amp;chksm=fd3c3d99ca4bb48f0b5e2684e10e4fd5d24b9a5dbe92f8fb04d811e96246fd942dfa9321df31&amp;scene=21#wechat_redirect" target="_blank" data-itemshowtype="0" data-linktype="2">看了那么多，TCP/IP究竟是什么（一）</a></p><p class="" style="white-space: normal;color: rgb(85, 85, 85);font-size: 14px;"><a href="http://mp.weixin.qq.com/s?__biz=MzU3MzcxMzg2Mw==&amp;mid=2247483677&amp;idx=1&amp;sn=989ba7afba4688698799dfc3fdb22207&amp;chksm=fd3c3db5ca4bb4a3019f234701bccb40a6c5a43eae6669b9835bf3e14b48cd8c7e3ce4d3b3fd&amp;scene=21#wechat_redirect" target="_blank" data-itemshowtype="0" data-linktype="2" style="font-size: 14px;white-space: normal;">TCP三次握手和四次分手</a></p><p class="" style="white-space: normal;color: rgb(85, 85, 85);font-size: 14px;"><a href="http://mp.weixin.qq.com/s?__biz=MzU3MzcxMzg2Mw==&amp;mid=2247483653&amp;idx=1&amp;sn=1366e3ed6daa4a5158968f44926a1cd4&amp;chksm=fd3c3dadca4bb4bbf8f4903d442d23d283600d553bc2edf04e9f49691871d429086a81e420ca&amp;scene=21#wechat_redirect" target="_blank" data-itemshowtype="0" data-linktype="2">vuejs 中双向绑定的模拟实现</a></p></section></section></section></section>

<br />

<section class="" data-style-type="5" data-tools="新媒体排版" data-id="2171798"><section style="margin: 0px;padding: 0px;border-width: 0px;border-style: initial;border-color: initial;max-width: 100%;text-align: center;overflow-wrap: break-word !important;box-sizing: border-box !important;"><section style="max-width: 100%;display: inline-block;overflow-wrap: break-word !important;box-sizing: border-box !important;"><section style="font-size: 16.3636px;max-width: 100%;display: inline-block;overflow-wrap: break-word !important;box-sizing: border-box !important;"><p style="margin-top: 0px;margin-bottom: 0px;max-width: 100%;word-wrap: normal;min-height: 1em;white-space: pre-wrap;box-sizing: border-box !important;"><strong style="box-sizing:border-box !important;max-width:100%;word-wrap:break-word !important;text-transform: uppercase;">·end·</strong><br style="max-width: 100%;word-wrap: break-word !important;box-sizing: border-box !important;"></p></section><section class="" style="font-size: 1.8em;text-align: center;max-width: 100%;margin: 0em 0.5em 0.1em;color: rgb(16, 146, 113);line-height: 1;font-weight: inherit;overflow-wrap: break-word !important;box-sizing: border-box !important;"><span style="font-size:20px;"><strong class="" style="border-bottom-style:solid;border-bottom-width:1px;border-color:rgb(2,2,2);box-sizing:border-box !important;color:rgb(16,146,113);display:block;font-size:20px;font-weight:bold;line-height:28px;max-width:100%;padding:0px 50px 3px;text-align:center;word-wrap:break-word !important;">止水lx</strong></span></section><section class="" style="max-width: 100%;margin: 0.5em 1em;line-height: 1;font-weight: inherit;text-align: inherit;text-decoration: inherit;color: rgb(120, 124, 129);overflow-wrap: break-word !important;box-sizing: border-box !important;"><p style=""><span style="font-size: 16.3636px;">一个想要分享点知识的公众号</span></p><p style="font-size: 1em;"><img class="" data-copyright="0" data-cropselx1="0" data-cropselx2="200" data-cropsely1="0" data-cropsely2="200" data-ratio="1" data-src="https://mmbiz.qpic.cn/mmbiz_jpg/s2mbdj9tvLaTBC415zhibJlFwOyAOVr01xzvNg4Wnpw1GYVBvly0Gm8WibicAHHprClKjiaHY6SodE2yticql5ucllg/640?wx_fmt=jpeg" data-type="jpeg" data-w="258" style="width: 200px !important; height: auto !important; visibility: visible !important;" _width="200px" src="https://qiniu1.lxfriday.xyz/WaterM/072481f0-c293-4252-9817-339ad5ef42d4_公众号二维码.webp" crossorigin="anonymous" data-fail="0"></p></section><span style="font-size: 1em;background-color: rgb(16, 146, 113);border-radius: 0.3em;box-shadow: rgb(16, 146, 113) 0.1em 0.1em 0.1em;color: white;display: inline-block;font-weight: inherit;max-width: 100%;padding: 0.3em;text-align: inherit;text-decoration: inherit;box-sizing: border-box !important;overflow-wrap: break-word !important;" class="">微信号：lxfriday_xyz</span></section></section></section>

