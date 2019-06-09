---
title: 新手拿到一台云服务器之后怎么连接上服务器？怎么添加用户？怎么连接FTP上传文件？怎么安装Node？
comments: true
top: false
date: 2019-06-09 23:15:26
categories: linux
tags: linux
img: https://qiniu1.lxfriday.xyz/WaterM/8f67a82f-f9e1-4c17-ad3d-32449389ce99_linux.jpg
---

> 正文约 1900 字，读完需要 5 分钟

“ 本文讲解新服务器的基本操作，涉及到连接服务器、添加用户、FTP 配置、配置 Node 环境、后台运行 Node 程序”



毕设答辩原本以为老师只会在我的电脑上查看操作过程，后来从同学那里得知老师要求能够通过校园网来访问项目。然后就又开始倒腾服务器了。



要倒腾服务器，首先得有一台能够自己完全控制的服务器，虚拟机不如服务器自由，玩的不过瘾。



说一个前提，如果想要带有域名也就是浏览器里面输入的那一串地址，需要提前购买和备案，否则要等半个月左右才能使用。



服务器厂商阿里云、腾讯云、百度云都可以，我用的阿里云。



选择一个低配的 1核CPU、2G内存，镜像代表操作系统，选择 Ubuntu，版本选择 16.04，系统盘使用默认 40G。



安全组按照默认的来，后面可以在服务器管理里面配置，管理开放的端口。

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/ea40f804-5e22-47c9-a939-8fdd49878bd8_640.webp" alt=""><p class="img-desc"></p></div>

<section class="" data-style-type="5" data-tools="新媒体排版" data-id="2183368"><section class="" style="border-width: 0px;border-style: none;border-color: initial;padding: 0px;"><section class="" style="box-sizing: border-box;" powered-by="KolEditor.us"><section class="" style="margin-top: 10px;margin-bottom: 10px;box-sizing: border-box;"><section class="" style="display: inline-block;box-sizing: border-box;"><span style="display: block;padding: 0.3em 0.5em;border-radius: 0.8em 0.8em 0px 0px;background-color: rgb(249, 110, 87);font-size: 14.08px;color: rgb(255, 255, 255);box-sizing: border-box;" class=""><section style="box-sizing: border-box;">1</section></span> </section><section class="" style="border-width: 1px;border-style: solid;border-color: rgb(249, 110, 87);border-radius: 0px 0px 0.8em 0.8em;padding: 10px;box-sizing: border-box;"><section class="" style="box-sizing: border-box;" powered-by="KolEditor.us"><section class="" style="box-sizing: border-box;"><section class="" style="box-sizing: border-box;"><section style="box-sizing: border-box;">怎么连接服务器</section></section></section></section></section></section></section></section></section>

在 Mac 上安装 ZenTermLite ，打开点击 + ，会话名称随便取，主机地址即为对公网的地址（下图中公对应的地址）。

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/94c5536a-8b36-466d-9b43-6d938fc2e0b6_640 (1).webp" alt=""><p class="img-desc"></p></div>

用户名用 root，点击认证，在密码中输入申请实例的时候创建的密码。

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/3081cf0d-c1c2-4a38-8e00-098186047baa_640 (2).webp" alt=""><p class="img-desc"></p></div>

点击确定，连接即可自动连接上服务器（以root身份）。

<section class="" data-style-type="5" data-tools="新媒体排版" data-id="2183368"><section class="" style="border-width: 0px;border-style: none;border-color: initial;padding: 0px;"><section class="" style="box-sizing: border-box;" powered-by="KolEditor.us"><section class="" style="margin-top: 10px;margin-bottom: 10px;box-sizing: border-box;"><section class="" style="display: inline-block;box-sizing: border-box;"><span style="display: block;padding: 0.3em 0.5em;border-radius: 0.8em 0.8em 0px 0px;background-color: rgb(249, 110, 87);font-size: 14.08px;color: rgb(255, 255, 255);box-sizing: border-box;" class=""><section style="box-sizing: border-box;">2</section></span> </section><section class="" style="border-width: 1px;border-style: solid;border-color: rgb(249, 110, 87);border-radius: 0px 0px 0.8em 0.8em;padding: 10px;box-sizing: border-box;"><section class="" style="box-sizing: border-box;" powered-by="KolEditor.us"><section class="" style="box-sizing: border-box;"><section class="" style="box-sizing: border-box;"><section style="box-sizing: border-box;">怎么添加新用户</section></section></section></section></section></section></section></section></section>

先查看系统的用户

```bash
cat /etc/passwd
```

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/dc478903-686a-4b9c-8e56-8d092652dc88_640 (3).webp" alt=""><p class="img-desc"></p></div>

我们创建一个用户 uftp，用来供上传文件使用。会要求输入密码。

```bash
adduser uftp
```

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/c4238cae-bf08-4b1e-be7c-7dd806cfc6d4_640 (4).webp" alt=""><p class="img-desc"></p></div>

这行命令会自动创建用户目录。

删除用户

```bash
userdel uftp
```

<section class="" data-style-type="5" data-tools="新媒体排版" data-id="2183368"><section class="" style="border-width: 0px;border-style: none;border-color: initial;padding: 0px;"><section class="" style="box-sizing: border-box;" powered-by="KolEditor.us"><section class="" style="margin-top: 10px;margin-bottom: 10px;box-sizing: border-box;"><section class="" style="display: inline-block;box-sizing: border-box;"><span style="display: block;padding: 0.3em 0.5em;border-radius: 0.8em 0.8em 0px 0px;background-color: rgb(249, 110, 87);font-size: 14.08px;color: rgb(255, 255, 255);box-sizing: border-box;" class=""><section style="box-sizing: border-box;">3</section></span> </section><section class="" style="border-width: 1px;border-style: solid;border-color: rgb(249, 110, 87);border-radius: 0px 0px 0.8em 0.8em;padding: 10px;box-sizing: border-box;"><section class="" style="box-sizing: border-box;" powered-by="KolEditor.us"><section class="" style="box-sizing: border-box;"><section class="" style="box-sizing: border-box;"><section style="box-sizing: border-box;">怎么通过FTP上传文件</section></section></section></section></section></section></section></section></section>

服务器需要先安装 ftp 服务，安装 vsftp。

```bash
apt install vsftpd
```

配置 vsftp。

```bash
vi /etc/vsftp.conf
```

在 `# Uncomment this to enable any form of FTP write command.` 处，添加如下语句

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/ef6e164d-be24-4f20-baf0-66d9ded33310_640 (5).webp" alt=""><p class="img-desc"></p></div>

同时创建 `/etc/vsftpd.user_list`

```bash
touch /etc/vsftpd.user_list
vi /etc/vsftpd.user_list
```

添加使用的用户，重启服务即可。

```bash
sudo /etc/init.d/vsftpd start  # 启动vsftp服务
sudo /etc/init.d/vsftpd stop # 停止vsftp服务
sudo /etc/init.d/vsftpd restart # 重启vsftp服务
```

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/ca6a5053-b702-4492-8953-e2d4eb51f282_640 (6).webp" alt=""><p class="img-desc"></p></div>

先下载一个 FIleZilla，这是一个 FTP客户端。输入

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/112b6686-53bd-41ef-91db-eb1ad4901099_640 (7).webp" alt=""><p class="img-desc"></p></div>

即可列出该目录，之后就可以开始上传下载文件了。

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/f41066c3-059e-4b21-90f0-36b020ef2276_640 (8).webp" alt=""><p class="img-desc"></p></div>

<section class="" data-style-type="5" data-tools="新媒体排版" data-id="2183368"><section class="" style="border-width: 0px;border-style: none;border-color: initial;padding: 0px;"><section class="" style="box-sizing: border-box;" powered-by="KolEditor.us"><section class="" style="margin-top: 10px;margin-bottom: 10px;box-sizing: border-box;"><section class="" style="display: inline-block;box-sizing: border-box;"><span style="display: block;padding: 0.3em 0.5em;border-radius: 0.8em 0.8em 0px 0px;background-color: rgb(249, 110, 87);font-size: 14.08px;color: rgb(255, 255, 255);box-sizing: border-box;" class=""><section style="box-sizing: border-box;">4</section></span> </section><section class="" style="border-width: 1px;border-style: solid;border-color: rgb(249, 110, 87);border-radius: 0px 0px 0.8em 0.8em;padding: 10px;box-sizing: border-box;"><section class="" style="box-sizing: border-box;" powered-by="KolEditor.us"><section class="" style="box-sizing: border-box;"><section class="" style="box-sizing: border-box;"><section style="box-sizing: border-box;">怎么安装Node</section></section></section></section></section></section></section></section></section>

Linux 上面选择从源文件编译安装，打开 http://nodejs.cn/download/，选择安装包。

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/fe311ab7-7810-4922-b9d4-9cb88b2f0df8_640 (9).webp" alt=""><p class="img-desc"></p></div>

找到 node-v10.16.0.tar.gz

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/500ca0af-62a0-4c9f-af53-fb5fe92ea038_640 (10).webp" alt=""><p class="img-desc"></p></div>

之后复制链接地址

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/05ddfd96-5a0d-4be1-8288-d30f15b0aa1a_640 (11).webp" alt=""><p class="img-desc"></p></div>

wget 将文件下载到机器上，tar 将文件解压

```bash
wget https://npm.taobao.org/mirrors/node/v10.16.0/node-v10.16.0.tar.gz
tar -zxvf node-v10.16.0.tar.gz 
```

以 root 用户的身份执行

```bash
./configure
make && make install
```

静静等待。。。整个编译安装过程会比较漫长。。。

等待执行完之后，在命令行里面输入

```bash
node -v
v10.16.0
```

显示出 Node 版本，即为安装成功。

<section class="" data-style-type="5" data-tools="新媒体排版" data-id="2183368"><section class="" style="border-width: 0px;border-style: none;border-color: initial;padding: 0px;"><section class="" style="box-sizing: border-box;" powered-by="KolEditor.us"><section class="" style="margin-top: 10px;margin-bottom: 10px;box-sizing: border-box;"><section class="" style="display: inline-block;box-sizing: border-box;"><span style="display: block;padding: 0.3em 0.5em;border-radius: 0.8em 0.8em 0px 0px;background-color: rgb(249, 110, 87);font-size: 14.08px;color: rgb(255, 255, 255);box-sizing: border-box;" class=""><section style="box-sizing: border-box;">5</section></span> </section><section class="" style="border-width: 1px;border-style: solid;border-color: rgb(249, 110, 87);border-radius: 0px 0px 0.8em 0.8em;padding: 10px;box-sizing: border-box;"><section class="" style="box-sizing: border-box;" powered-by="KolEditor.us"><section class="" style="box-sizing: border-box;"><section class="" style="box-sizing: border-box;"><section style="box-sizing: border-box;">如何后台运行 Node 程序</section></section></section></section></section></section></section></section></section>

使用 pm2 可以让 Node 程序运行在后台，即使关闭命令行也可以继续运行。

```bash
npm i -g pm2
```

启动类似 `node xxx.js` 的项目

```bash
pm2 start --name xxxsname xxx.js  # 将运行的实例命名为 xxxsname
```

启动类似 `npm run serve` 的项目

```bash
pm2 start --name servename npm -- run serve
```

以配置文件的形式启动

```bash
pm2 start pm2config.json
```

配置文件如下

```bash
{
  "apps": [{
    "name": "appname", # 应用名
    "exec_interpreter": "node", # 执行环境
    "script": "./b.js", # 要执行的脚本
    "cwd": "/home/uftp/test-pm2", # 项目路径
    "exec_mode": "fork",
    "max_memory_restart": "1G",
    "autorestart": true, # 出错自动重启
    "node_args": [],
    "error_file" : "./test-err.log", # 错误日志文件位置
    "out_file": "./test-out.log",  # 输出日志文件位置
    "pid_file": "./test.pid",  # 进程相关文件位置
    "args": [],
    "env": {
    }
  }]
}
```

pm2 真的是后台自动部署的神器！！！

---


<section class="" data-style-type="5" data-tools="新媒体排版" data-id="2177544" style="white-space: normal;"><p class="" style="max-width: 100%;box-sizing: border-box;min-height: 1em;text-align: center;overflow-wrap: break-word !important;"><span data-txtless="spin" data-txtlessp="120" style="font-size: 14px;max-width: 100%;color: rgb(228, 157, 172);box-sizing: border-box !important;overflow-wrap: break-word !important;">◆&nbsp;</span><span style="font-size: 14px;max-width: 100%;color: rgb(250, 211, 172);box-sizing: border-box !important;overflow-wrap: break-word !important;">◆&nbsp;</span><span data-txtless="spin" data-txtlessp="-120" style="font-size: 14px;max-width: 100%;color: rgb(255, 233, 234);box-sizing: border-box !important;overflow-wrap: break-word !important;">◆ &nbsp;<span data-txtless="spin" data-txtlessp="120" style="max-width: 100%;color: rgb(121, 134, 175);box-sizing: border-box !important;overflow-wrap: break-word !important;">◆&nbsp;</span><span style="max-width: 100%;color: rgb(17, 163, 215);box-sizing: border-box !important;overflow-wrap: break-word !important;">◆</span></span></p></section>

<p style="white-space: normal;"><br></p>

<section class="" data-style-type="5" data-tools="新媒体排版" data-id="2170287" style="white-space: normal;"><section style="margin-right: 15px;margin-bottom: 10px;margin-left: 15px;float: left;color: inherit;"><p style="color: inherit;line-height: 72px;"><span style="color: rgb(95, 156, 239);font-size: 72px;">09</span></p><p style="color: rgb(153, 153, 153);"><span style="color: inherit;font-size: 24px;">06,2019</span></p></section><section style="margin: 24px 5px -20px -8px;border-right: 10px solid rgb(95, 156, 239);border-left-width: 0px;border-left-color: rgb(95, 156, 239);display: inline-block;max-width: 100%;height: 9px;width: 9px;vertical-align: top;float: left;border-bottom: 10px solid transparent !important;border-top-style: solid !important;border-top-color: transparent !important;overflow-wrap: break-word !important;box-sizing: border-box !important;"></section><section class="" style="margin-left: 125px;padding: 15px;line-height: 1.75em;color: rgb(255, 255, 255);border-radius: 5px;background: rgb(95, 156, 239);">这是一篇比较基础的入门教程，感觉能给初学者节省半天到一天的时间去熟悉吧！后面会讲解 Nginx 做静态文件服务器和反向代理服务器！</section></section>

<p style="white-space: normal;"><br></p>
<p style="white-space: normal;"><br></p>

<section class="" data-style-type="1" data-tools="新媒体排版" data-id="12893" style="white-space: normal;"><section class="" style="margin-top: 20px;"><section style="margin: 20px 5px 5px;padding: 5px;border-width: 2px;border-style: solid;border-color: rgb(240, 84, 84);border-radius: 4px;"><section style="display: flex;justify-content: center;"><section style="margin-top: -20px;padding-right: 25px;padding-left: 25px;background-color: rgb(254, 254, 254);"><p class="" style="color: rgb(240, 84, 84);font-size: 18px;min-width: 1em;">推荐阅读</p></section></section><section style="padding: 20px;"><p class="" style="color: rgb(85, 85, 85);font-size: 14px;"><a href="http://mp.weixin.qq.com/s?__biz=MzU3MzcxMzg2Mw==&amp;mid=2247483703&amp;idx=1&amp;sn=be296a8a00cd49ffda228806ec2c2d8f&amp;chksm=fd3c3d9fca4bb4894089132bab1f2fbb8c99cf69ec0698ea09d907210fdaca2424500bb845f2&amp;scene=21#wechat_redirect" target="_blank" data-itemshowtype="0" data-linktype="2">看了那么多，TCP/IP究竟是什么（二）</a></p><p class="" style="color: rgb(85, 85, 85);font-size: 14px;"><a href="http://mp.weixin.qq.com/s?__biz=MzU3MzcxMzg2Mw==&amp;mid=2247483697&amp;idx=1&amp;sn=a35ff697f4b57f6d42d58347e51f3ea7&amp;chksm=fd3c3d99ca4bb48f0b5e2684e10e4fd5d24b9a5dbe92f8fb04d811e96246fd942dfa9321df31&amp;scene=21#wechat_redirect" target="_blank" data-itemshowtype="0" data-linktype="2">看了那么多，TCP/IP究竟是什么（一）</a><br></p><p class="" style="color: rgb(85, 85, 85);font-size: 14px;"><a href="http://mp.weixin.qq.com/s?__biz=MzU3MzcxMzg2Mw==&amp;mid=2247483677&amp;idx=1&amp;sn=989ba7afba4688698799dfc3fdb22207&amp;chksm=fd3c3db5ca4bb4a3019f234701bccb40a6c5a43eae6669b9835bf3e14b48cd8c7e3ce4d3b3fd&amp;scene=21#wechat_redirect" target="_blank" data-itemshowtype="0" data-linktype="2">TCP三次握手和四次分手</a></p><p class="" style="color: rgb(85, 85, 85);font-size: 14px;"><a href="http://mp.weixin.qq.com/s?__biz=MzU3MzcxMzg2Mw==&amp;mid=2247483653&amp;idx=1&amp;sn=1366e3ed6daa4a5158968f44926a1cd4&amp;chksm=fd3c3dadca4bb4bbf8f4903d442d23d283600d553bc2edf04e9f49691871d429086a81e420ca&amp;scene=21#wechat_redirect" target="_blank" data-itemshowtype="0" data-linktype="2">vuejs 中双向绑定的模拟实现</a></p></section></section></section></section>

<p style="white-space: normal;"><br></p>

<section style="white-space: normal;font-family: 微软雅黑;font-size: 15px;letter-spacing: 2px;text-align: center;max-width: 100%;box-sizing: border-box;display: inline-block;overflow-wrap: break-word !important;"></section>

<p style="white-space: normal;"><br></p>

<section class="" data-style-type="4" data-tools="新媒体排版" data-id="9499" style="white-space: normal;"><section class=""><section style="margin-right: auto;margin-left: auto;text-align: center;"><section style="margin-right: auto;margin-left: auto;padding-bottom: 10px;font-size: 0px;max-width: 360px !important;"><section style="padding-left: 2px;width: 65px;"><img class="" data-ratio="0.27380952380952384"  data-type="jpeg" data-w="84" style="vertical-align: bottom; width: 84px !important; height: auto !important; visibility: visible !important;" _width="84px" src="https://qiniu1.lxfriday.xyz/WaterM/4ed0d012-33c7-4e3b-aae8-a299cc689477_640 (12).webp" crossorigin="anonymous" data-fail="0"></section><section style="margin-top: -10px;padding: 30px 25px;border-width: 2px;border-style: solid;border-color: rgb(255, 138, 101);display: flex;align-items: flex-start;"><section style="flex-shrink: 0;"><section style="width: 75px;"><img class="" data-copyright="0" data-cropselx1="0" data-cropselx2="200" data-cropsely1="0" data-cropsely2="200" data-ratio="1" data-type="jpeg" data-w="258" style="color: rgb(120, 124, 129); width: 75px !important; height: auto !important; visibility: visible !important;" _width="75px" src="https://qiniu1.lxfriday.xyz/WaterM/072481f0-c293-4252-9817-339ad5ef42d4_公众号二维码.webp" crossorigin="anonymous" data-fail="0"></section></section><section style="padding-left: 15px;flex-shrink: 1;"><p style="margin-right: auto;margin-left: auto;text-align: left;font-size: 18px;line-height: 1.7;">止水聊技术</p><p style="margin-right: auto;margin-left: auto;text-align: left;font-size: 16px;line-height: 1.4;">关注回复关键词送学习资料,帮你快速掌握刚需技能</p></section></section><section style="margin-top: -10px;padding-right: 2px;width: 65px;float: right;"><img class="" data-ratio="0.27380952380952384"  data-type="jpeg" data-w="84" style="vertical-align: bottom; width: 84px !important; height: auto !important; visibility: visible !important;" _width="84px" src="https://qiniu1.lxfriday.xyz/WaterM/17187d89-b54f-4546-947c-4237743692c1_640 (14).webp" crossorigin="anonymous" data-fail="0"></section><section style="margin-top: -18px;margin-right: auto;margin-left: 70px;padding-right: 15px;padding-left: 15px;height: 30px;line-height: 30px;border-radius: 15px;background-color: rgb(255, 138, 101);display: inline-block;box-sizing: border-box;"><section style="display: flex;align-items: center;"><section style="display: flex;align-items: center;width: 20px;"><img class="" data-ratio="1"  data-type="jpeg" data-w="132" style="vertical-align: top; width: 132px !important; height: auto !important; visibility: visible !important;" _width="132px" src="https://qiniu1.lxfriday.xyz/WaterM/3e7be4c8-af93-44d1-bfba-b49ddac09688_640 (13).webp" crossorigin="anonymous" data-fail="0"></section><p style="margin-left: 10px;color: rgb(255, 255, 255);font-size: 18px;">关注</p></section></section></section></section></section></section>





