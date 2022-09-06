---
layout:     post
title:      【Mac | Github】Mac解决Github访问慢解决办法
subtitle:   【Mac | Github】Mac解决Github访问慢解决办法
date:       2022-03-24 09:59:51
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Mac
    - macos
    - github
---

>【Mac | Github】Mac解决Github访问慢解决办法

# 【Mac | Github】Mac解决Github访问慢解决办法


**目录**

[前言](#%E5%89%8D%E8%A8%80)

[1. 打开下面的网站](#1.%20%E6%89%93%E5%BC%80%E4%B8%8B%E9%9D%A2%E7%9A%84%E7%BD%91%E7%AB%99)

[2. 输入查询 github.global.ssl.fastly.net 和 github.com 两个地址 ​​](#2.%20%E8%BE%93%E5%85%A5%E6%9F%A5%E8%AF%A2%20github.global.ssl.fastly.net%20%E5%92%8C%20github.com%20%E4%B8%A4%E4%B8%AA%E5%9C%B0%E5%9D%80%C2%A0%E2%80%8B%E2%80%8B)

[ 3. 修改host 可以直接下载下面的软件：](#%C2%A03.%20%E4%BF%AE%E6%94%B9host%20%E5%8F%AF%E4%BB%A5%E7%9B%B4%E6%8E%A5%E4%B8%8B%E8%BD%BD%E4%B8%8B%E9%9D%A2%E7%9A%84%E8%BD%AF%E4%BB%B6%EF%BC%9A)

[4. 看效果](#4.%20%E7%9C%8B%E6%95%88%E6%9E%9C)

----

# 前言

我查这个完全是因为我使用electron-egg打包exe时候访问GitHub下载文件下载不到！

使用下面方法解决了，下面直接开干！

# 1. 打开下面的网站

[IPAddress.com![](https://csdnimg.cn/release/blog_editor_html/release2.0.8/ckeditor/plugins/CsdnLink/icons/icon-default.png?t=M276)https://www.ipaddress.com/](https://www.ipaddress.com/ "IPAddress.com")

# 2. 输入查询 **github.global.ssl.fastly.net** 和 **github.com** 两个地址 ![](https://img-blog.csdnimg.cn/8284956eb7be418e9657a6eb98b2a65b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Yy65Z2X6ZO-5byA5Y-R5bel56iL5biI,size_20,color_FFFFFF,t_70,g_se,x_16)![](https://img-blog.csdnimg.cn/eaff30be70804349abe2684b1b5e1a78.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Yy65Z2X6ZO-5byA5Y-R5bel56iL5biI,size_20,color_FFFFFF,t_70,g_se,x_16)

#  3. 修改host 可以直接下载下面的软件：

![](https://img-blog.csdnimg.cn/b83bd339229448b891bc4543912e8b70.png)

![](https://img-blog.csdnimg.cn/3c6de7c0dcf644d89b39a64da54b79c4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Yy65Z2X6ZO-5byA5Y-R5bel56iL5biI,size_17,color_FFFFFF,t_70,g_se,x_16)

修改完之后保存

# 4. 看效果

 修改之前ping

 ![](https://img-blog.csdnimg.cn/d923ec4971ec41fda98e2051e59b552f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Yy65Z2X6ZO-5byA5Y-R5bel56iL5biI,size_10,color_FFFFFF,t_70,g_se,x_16)

修改之后ping 

![](https://img-blog.csdnimg.cn/bcccc23e5498479e93cf17e49d9fd537.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Yy65Z2X6ZO-5byA5Y-R5bel56iL5biI,size_10,color_FFFFFF,t_70,g_se,x_16)

 打包完成，over！![](https://img-blog.csdnimg.cn/35c72c88c797467181d8aa50d4b5f8d4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Yy65Z2X6ZO-5byA5Y-R5bel56iL5biI,size_20,color_FFFFFF,t_70,g_se,x_16)


