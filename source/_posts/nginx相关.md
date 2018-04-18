---
title: nginx相关
comments: false
date: 2018-04-18 12:29:25
categories: 后端
tags: nginx
---

## 文档

- [官网](http://nginx.org/en)

- [常见命令](http://nginx.org/en/docs/windows.html)


## 在ubuntu安装教程

- [安装及操作教程](https://www.jianshu.com/p/7cb1a824333e)

- [操作过程](https://blog.csdn.net/hanshileiai/article/details/54317276)


## ssl配置选项

```bash
server {
    listen 443;
    server_name localhost;
    ssl on;
    root html;
    index index.html index.htm;
    ssl_certificate   cert/1523087864255.pem;
    ssl_certificate_key  cert/1523087864255.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location / {
        root html;
        index index.html index.htm;
    }
}

```

## 注意

1、配置好之后，先检验配置文件的正确性，再重启nginx,
```bash
nginx -t

service nginx reload
```

2、若启用ssl，需开启服务器的443端口


## 其他

```bash
# 查看端口
netstat -ntpl
```


[配置文件例子](http://qiniu1.lxfriday.xyz/other/hzauhelper)