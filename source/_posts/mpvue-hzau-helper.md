---
title: mpvue-hzau-helper
comments: false
date: 2018-06-05 18:35:00
categories: 微信小程序
tags: mpvue-hzau-helper
---

[项目地址](https://github.com/lxfriday/mpvue-hzau-helper)

<div style="text-align:center;margin: 20px">
    <img src="http://qiniu1.lxfriday.xyz/hzauhelper/IMG_0766.JPG" width=300 >
    <span style="margin: 20px; color: #999;font-size:14px">小程序二维码</span>
</div>

## 前言

小程序从开始着手设计，到现在稳定运行已经过了3个月。累计服务用户数超过了1400人。

整个项目从UI设计到小程序代码、后端抓取代码全部由我一个人完成。工作量还是挺大的。1.X版本的广场功能因为微信官方不允许，到2.X就整块全部删除了。成了现在的纯查询工具。

## 小程序介绍

### Tab

- 华农地图
- 华农特色地点
- 华农校内服务
- 关于

### 所有page

- `pages/map/map` 华农地图，对华农周边的主要地点进行标注，并可依据类型进行查看
- `pages/places/places` 华农特色地点，简介华农的一些特色地点
- `pages/placedetail/placedetail`  华农特色地点详情
- `pages/schoolservice/schoolservice` 华农校内服务，提供体育管理系统体测成绩、学生考试成绩、等级考试成绩、考试信息、宿舍电费查询
- `pages/electriccharge/electriccharge` 电费查询
- `pages/jwcsignin/jwcsignin` 登录教务管理系统
- `pages/jwcrankexamination/jwcrankexamination` 等级考试查询
- `pages/jwctermexamination/jwctermexamination` 学期考试信息查询
- `pages/jwctermgpa/jwctermgpa` 所有学期的学期成绩查询
- `pages/pegrades/pegrades` 体育管理系统成绩查询
- `pages/about/about` 关于

### 使用到的关键技术

- [mpvue](https://github.com/Meituan-Dianping/mpvue)
- [Vue](https://github.com/vuejs/vue)
- [Vuex](https://github.com/vuejs/vuex)
- [WeUI](https://github.com/Tencent/weui-wxss)
- [mpvue-weui](https://github.com/KuangPF/mpvue-weui)
