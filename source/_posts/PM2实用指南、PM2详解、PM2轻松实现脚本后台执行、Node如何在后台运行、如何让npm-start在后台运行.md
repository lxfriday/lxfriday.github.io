---
title: PM2实用指南、PM2详解、PM2轻松实现脚本后台执行、Node如何在后台运行、如何让npm start在后台运行
comments: true
top: false
date: 2019-06-10 12:59:04
categories: pm2
tags: pm2
img: https://qiniu1.lxfriday.xyz/WaterM/ff22e15f-8365-4158-87bc-0774af30973f_WX20190610-130037-pm2.png
---

如何让已经写好的 Node 代码在后台运行呢？即使关闭了命令行也能运行，这怎么做到呢？这里推荐一个工具 PM2

[PM2 官网](https://pm2.io)
[PM2 GitHub](https://github.com/Unitech/pm2)

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/bfb5a74a-2de9-4c4e-8edf-c63d75145da6_20190610100658325.png" alt=""><p class="img-desc"></p></div>

大概解释下官网的介绍

PM2 是一个应用管理器，它自身具备负载均衡能力，它可以让应用在后台一直运行，也可以让无缝重启应用，中间不会停止服务，可以开机启动，对开发者来讲可以极大地方便应用部署，降低维护成本。

## 安装
```bash
npm i -g pm2
```

## 启动
```bash
pm2 start app.js --watch   #实时监控app.js的方式启动，当app.js文件有变动时，pm2会自动reload
pm2 start app.js -i max  # 根据有效CPU数目启动最大进程数目
pm2 start app.js -n appname -i max -e err.log -o out.log # 以 appname 启动 app.js，错误当前目录 err.log 为 error log，out.log 为输出 log
pm2 start bb.sh  --interpreter bash # 用 bash 执行脚本
pm2 start test.py--interpreter python3 # 用 python 环境执行脚本
```

启动类似 node xxx.js 的项目

```bash
pm2 start --name xxxsname xxx.js  # 将运行的实例命名为 xxxsname
```

启动类似 npm start 的项目

```bash
pm2 start --name appname npm -- start # 设置应用名为 appname
```

启动类似 npm run serve 的项目

```bash
pm2 start --name servename npm -- run serve # 设置应用名为 servename
```

--name xxx 或者 -n xxx 表示将应用命名为 xxx

以配置文件的形式启动

```bash
pm2 start pm2config.json
```

## 查看 PM2 部署的应用

```bash
pm2 list               # 显示所有进程状态
pm2 ls                 # 显示所有进程状态
pm2 show 0			   # 显示某个应用的详细信息
pm2 monit              # 监视所有进程
pm2 logs               # 显示所有进程日志
pm2 log 0 	           # 查看 0 应用的日志
pm2 stop all           # 停止所有进程
pm2 restart all        # 重启所有进程
pm2 reload all         # 0秒停机重载进程
pm2 stop 0             # 停止指定的进程，0 是应用 id
pm2 restart 0          # 重启指定的进程，0 是应用 id
pm2 startup            # 产生 init 脚本 保持进程活着，startup 是指系统boot, 开机进程自启动
pm2 unstartup          # 禁用开机进程自启动
pm2 delete 0           # 杀死指定的进程，0 是应用 id，会删除该应用
pm2 delete all         # 杀死全部进程，会删除所有应用
```

`pm2 list`

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/9833beff-c62d-4efa-8742-32af32024f6d_20190610104031393.png" alt=""><p class="img-desc"></p></div>

`pm2 show`

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/6dbb6455-ddae-41cd-b625-7581430893aa_20190610104122473.png" alt=""><p class="img-desc"></p></div>

`pm2 monit`

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/59275bf6-4e15-432b-84b9-ef4b0b5af9b7_20190610104150751.png" alt=""><p class="img-desc"></p></div>

`pm2 logs`

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/3e631dcb-2936-4b10-9894-c38b7b05830a_20190610104222553.png" alt=""><p class="img-desc"></p></div>

`pm2 log bb`

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/692e5274-95c7-44fb-9cb9-1d0440da339b_20190610121507570.png" alt=""><p class="img-desc"></p></div>

## PM2 文件目录

PM2 文件默认放在 $HOME/.pm2

<div class=img-center><img src="https://qiniu1.lxfriday.xyz/WaterM/faffe232-7275-43d2-bdb8-166511db9931_2019061012172324.png" alt=""><p class="img-desc"></p></div>

## 自定义启动文件

```json
{
  "apps": [{
    "name": "appname",
    "exec_interpreter": "node",
    "script": "./b.js",
    "cwd": "/home/uftp/test-pm2",
    "exec_mode": "fork",
    "max_memory_restart": "1G",
    "autorestart": true,
    "node_args": [],
    "watch": false
    "error_file" : "./test-err.log",
    "out_file": "./test-out.log",
    "pid_file": "./test.pid",
    "env": {
       "NODE_ENV": "development"
    },
    "min_uptime": "60s",
    "max_restarts": 30
  }]
}
```

`apps`: 是一个数组，数组中的每一个对象表示一个运行中的应用，可以配置多个应用，同时启动
`exec_interpreter` 执行环境，`node` `bash` `python` 等等
`script`: 将要执行的脚本
`cwd`: 应用的路径
`exec_mode`: 应用程序启动模式，`默认是fork`，`也可设置cluster_mode`
`max_memory_restart`: 超过这个指定的值之后将会重启应用 `1G` `500M`
`autorestart`: 出现故障之后是否自动重启，默认 `true`
`watch`: 监听文件更改，更改文件之后自动重启
`error_file`: error log 错误日志
`out_file`: out log 输出日期
`pid_file`: 进程文件
`min_uptime`: 最短的运行时间，如果少于这个时间就退出了，则会触发 `max_restarts`
`max_restarts`: 最大重启次数

<section class="" data-style-type="5" data-tools="新媒体排版" data-id="2177544" style="white-space: normal;"><p class="" style="max-width: 100%;box-sizing: border-box;min-height: 1em;text-align: center;overflow-wrap: break-word !important;"><span data-txtless="spin" data-txtlessp="120" style="font-size: 14px;max-width: 100%;color: rgb(228, 157, 172);box-sizing: border-box !important;overflow-wrap: break-word !important;">◆&nbsp;</span><span style="font-size: 14px;max-width: 100%;color: rgb(250, 211, 172);box-sizing: border-box !important;overflow-wrap: break-word !important;">◆&nbsp;</span><span data-txtless="spin" data-txtlessp="-120" style="font-size: 14px;max-width: 100%;color: rgb(255, 233, 234);box-sizing: border-box !important;overflow-wrap: break-word !important;">◆ &nbsp;<span data-txtless="spin" data-txtlessp="120" style="max-width: 100%;color: rgb(121, 134, 175);box-sizing: border-box !important;overflow-wrap: break-word !important;">◆&nbsp;</span><span style="max-width: 100%;color: rgb(17, 163, 215);box-sizing: border-box !important;overflow-wrap: break-word !important;">◆</span></span></p></section>

<p style="white-space: normal;"><br></p>

<section class="" data-style-type="1" data-tools="新媒体排版" data-id="12893" style="white-space: normal;"><section class="" style="margin-top: 20px;"><section style="margin: 20px 5px 5px;padding: 5px;border-width: 2px;border-style: solid;border-color: rgb(240, 84, 84);border-radius: 4px;"><section style="display: flex;justify-content: center;"><section style="margin-top: -20px;padding-right: 25px;padding-left: 25px;background-color: rgb(254, 254, 254);"><p class="" style="color: rgb(240, 84, 84);font-size: 18px;min-width: 1em;">推荐阅读</p></section></section><section style="padding: 20px;"><p class="" style="color: rgb(85, 85, 85);font-size: 14px;"><a href="http://mp.weixin.qq.com/s?__biz=MzU3MzcxMzg2Mw==&amp;mid=2247483703&amp;idx=1&amp;sn=be296a8a00cd49ffda228806ec2c2d8f&amp;chksm=fd3c3d9fca4bb4894089132bab1f2fbb8c99cf69ec0698ea09d907210fdaca2424500bb845f2&amp;scene=21#wechat_redirect" target="_blank" data-itemshowtype="0" data-linktype="2">看了那么多，TCP/IP究竟是什么（二）</a></p><p class="" style="color: rgb(85, 85, 85);font-size: 14px;"><a href="http://mp.weixin.qq.com/s?__biz=MzU3MzcxMzg2Mw==&amp;mid=2247483697&amp;idx=1&amp;sn=a35ff697f4b57f6d42d58347e51f3ea7&amp;chksm=fd3c3d99ca4bb48f0b5e2684e10e4fd5d24b9a5dbe92f8fb04d811e96246fd942dfa9321df31&amp;scene=21#wechat_redirect" target="_blank" data-itemshowtype="0" data-linktype="2">看了那么多，TCP/IP究竟是什么（一）</a><br></p><p class="" style="color: rgb(85, 85, 85);font-size: 14px;"><a href="http://mp.weixin.qq.com/s?__biz=MzU3MzcxMzg2Mw==&amp;mid=2247483677&amp;idx=1&amp;sn=989ba7afba4688698799dfc3fdb22207&amp;chksm=fd3c3db5ca4bb4a3019f234701bccb40a6c5a43eae6669b9835bf3e14b48cd8c7e3ce4d3b3fd&amp;scene=21#wechat_redirect" target="_blank" data-itemshowtype="0" data-linktype="2">TCP三次握手和四次分手</a></p><p class="" style="color: rgb(85, 85, 85);font-size: 14px;"><a href="http://mp.weixin.qq.com/s?__biz=MzU3MzcxMzg2Mw==&amp;mid=2247483653&amp;idx=1&amp;sn=1366e3ed6daa4a5158968f44926a1cd4&amp;chksm=fd3c3dadca4bb4bbf8f4903d442d23d283600d553bc2edf04e9f49691871d429086a81e420ca&amp;scene=21#wechat_redirect" target="_blank" data-itemshowtype="0" data-linktype="2">vuejs 中双向绑定的模拟实现</a></p></section></section></section></section>

<p style="white-space: normal;"><br></p>

<section style="white-space: normal;font-family: 微软雅黑;font-size: 15px;letter-spacing: 2px;text-align: center;max-width: 100%;box-sizing: border-box;display: inline-block;overflow-wrap: break-word !important;"></section>

<p style="white-space: normal;"><br></p>

<section class="" data-style-type="4" data-tools="新媒体排版" data-id="9499" style="white-space: normal;"><section class=""><section style="margin-right: auto;margin-left: auto;text-align: center;"><section style="margin-right: auto;margin-left: auto;padding-bottom: 10px;font-size: 0px;max-width: 360px !important;"><section style="padding-left: 2px;width: 65px;"><img class="" data-ratio="0.27380952380952384"  data-type="jpeg" data-w="84" style="vertical-align: bottom; width: 84px !important; height: auto !important; visibility: visible !important;" _width="84px" src="https://qiniu1.lxfriday.xyz/WaterM/4ed0d012-33c7-4e3b-aae8-a299cc689477_640 (12).webp" crossorigin="anonymous" data-fail="0"></section><section style="margin-top: -10px;padding: 30px 25px;border-width: 2px;border-style: solid;border-color: rgb(255, 138, 101);display: flex;align-items: flex-start;"><section style="flex-shrink: 0;"><section style="width: 75px;"><img class="" data-copyright="0" data-cropselx1="0" data-cropselx2="200" data-cropsely1="0" data-cropsely2="200" data-ratio="1" data-type="jpeg" data-w="258" style="color: rgb(120, 124, 129); width: 75px !important; height: auto !important; visibility: visible !important;" _width="75px" src="https://qiniu1.lxfriday.xyz/WaterM/072481f0-c293-4252-9817-339ad5ef42d4_公众号二维码.webp" crossorigin="anonymous" data-fail="0"></section></section><section style="padding-left: 15px;flex-shrink: 1;"><p style="margin-right: auto;margin-left: auto;text-align: left;font-size: 18px;line-height: 1.7;">止水聊技术</p><p style="margin-right: auto;margin-left: auto;text-align: left;font-size: 16px;line-height: 1.4;">关注回复关键词送学习资料,帮你快速掌握刚需技能</p></section></section><section style="margin-top: -10px;padding-right: 2px;width: 65px;float: right;"><img class="" data-ratio="0.27380952380952384"  data-type="jpeg" data-w="84" style="vertical-align: bottom; width: 84px !important; height: auto !important; visibility: visible !important;" _width="84px" src="https://qiniu1.lxfriday.xyz/WaterM/17187d89-b54f-4546-947c-4237743692c1_640 (14).webp" crossorigin="anonymous" data-fail="0"></section><section style="margin-top: -18px;margin-right: auto;margin-left: 70px;padding-right: 15px;padding-left: 15px;height: 30px;line-height: 30px;border-radius: 15px;background-color: rgb(255, 138, 101);display: inline-block;box-sizing: border-box;"><section style="display: flex;align-items: center;"><section style="display: flex;align-items: center;width: 20px;"><img class="" data-ratio="1"  data-type="jpeg" data-w="132" style="vertical-align: top; width: 132px !important; height: auto !important; visibility: visible !important;" _width="132px" src="https://qiniu1.lxfriday.xyz/WaterM/3e7be4c8-af93-44d1-bfba-b49ddac09688_640 (13).webp" crossorigin="anonymous" data-fail="0"></section><p style="margin-left: 10px;color: rgb(255, 255, 255);font-size: 18px;">关注</p></section></section></section></section></section></section>


