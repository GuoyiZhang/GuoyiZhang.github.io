---
layout:     post
title:      【Docker】「实战篇」开源项目docker化运维部署（终结篇）（十一）
subtitle:   【Docker】「实战篇」开源项目docker化运维部署（终结篇）（十一）
date:       2019-07-04 09:12:31
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 开发工具
    - 「实战篇」开源项目docker化运维部署
---

>【Docker】「实战篇」开源项目docker化运维部署（终结篇）（十一）

# 【Docker】「实战篇」开源项目docker化运维部署（终结篇）（十一）

最早系统部署到自己的服务器，有虚拟IP，可以完成热备，大概是2013年的时候，公司的服务器要升级到云端放到阿里云上，阿里云没有虚拟ip，keepalived没办法完成热备。只能通过nginx来进行负载完成十几台机器的负载。也有nginx挂的时候，2014年，面试认识了个大哥，建议接触下docker。于是自己搭建虚拟网络，学习至今，发现docker-swarm方便实在想热备就可以热备。通过docker-swarm得虚拟网络--net 多台机器轻松互联，容器挂了自动重启。如果知道Docker可以这样用，你就会彻底爱上Docker！

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMTIyMzcxNS02MTBjNDM0ZWI2YTFlM2ExLnBuZz9pbWFnZU1vZ3IyL2F1dG8tb3JpZW50L3N0cmlwJTdDaW1hZ2VWaWV3Mi8yL3cvNjAwL2Zvcm1hdC93ZWJw?x-oss-process=image/format,png)

docker的感悟
有老铁问我，买电脑thinkpad还是mac，我强烈用建议使用mac，安装个docker环境，随时安装各种的容器，方便自己用，自己写写shell，美滋滋比windows10，老更新开心多了，100g的C盘过几天就没了。

1. 这次主要做的前后端分离的项目，高级的专辑说的是微服务的项目
1. 编排真的需要吗？没用服务编排就没排面吗？老铁看你个人需求，没有最好的只有最适合的。
1. docker太省事了，站在别人的镜像里面搬自己砖
1. 良好的移植性，你做好的镜像打成包稳，到其他环境继续执行
1. 应用 Docker 时，你不仅是在分布你的代码，也是在分布你的代码所运行的环境
1. 用Docker的logo来解释，鲸鱼和集装箱，鱼中的大哥鲸鱼，慢慢的运载集装箱。
1. 服务的容灾性好，挂了自动重启，重启只是一个点
1. 古人云：有容乃大。是吧，容器就是docker哦
1. 未来在应用的开发测试，编译构建，和部署运行等环境，都使用Docker容器，并利用服务编排来管理容器集群。

PS：太多好处，没必要总结10个点。生命不止，学习不断！ 笔者阿明，因为也需要工作，目前工作中用到docker的很少，都是利用下班时间来学习分享。兴趣让我爱上了docker！会继续这条路，毕竟也是做开发的，工作中遇到的新技术也会继续分享给老铁，感谢老铁的支持。docker的专辑不会停止，会继续做下去。k8s我还没玩够呢！下次的目前目标是python爬虫借助docker技术。没茅台！来不及握手！ 拜了个拜！

