---
layout:     post
title:      【Docker】「实战篇」开源项目docker化运维部署-源码介绍（二）
subtitle:   【Docker】「实战篇」开源项目docker化运维部署-源码介绍（二）
date:       2019-07-03 17:17:47
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 「实战篇」开源项目docker化运维部署
---

>【Docker】「实战篇」开源项目docker化运维部署-源码介绍（二）

# 【Docker】「实战篇」开源项目docker化运维部署-源码介绍（二）

本次一起了解下人人网前后端开源项目， 之前也说过，前后端分离的特点和目标就是为了高可用，高负载，高性能的三高特点。公司都有自己的前后端分离框架，因为都签署的保密协议，也不好拿出来讲，就找了一个相对比较代码质量非常高出身名门的优秀框架：人人开源前后端框架。

![](//upload-images.jianshu.io/upload_images/11223715-83fdf4a8d5b81c67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

人人开源项目
官网：[https://www.renren.io/community/project](https://www.renren.io/community/project) 咱们选用 renren-fast这个框架。前台选用了vue主流前端框架，后端用的是老铁门都熟悉的java。

* 一个轻量级的Java快速开发平台，能快速开发项目并交付【接私活利器】
* 完善的XSS防范及脚本过滤，彻底杜绝XSS攻击
* 实现前后端分离，通过token进行数据交互
-实现管理员列表、角色管理、菜单管理、定时任务、参数管理、系统日志、文件上传(云存储)等功能
文档丰富：[https://www.renren.io/guide//#getdoc](https://www.renren.io/guide/#getdoc)

![](//upload-images.jianshu.io/upload_images/11223715-4e50159aa4c23435.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 后端技术的特点
大家都是开发出身。详细的我就不说了。

![](//upload-images.jianshu.io/upload_images/11223715-bd5a4274236d6c77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 前端技术的特点

![](//upload-images.jianshu.io/upload_images/11223715-e612ba35461f5a43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/625/format/webp)

PS：技术特点我都不做阐述了，重点是要把这个项目放入到docker虚拟机里面。这是最终的目的。

作者：IT人故事会
链接：https://www.jianshu.com/p/c66e36c736c4
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

