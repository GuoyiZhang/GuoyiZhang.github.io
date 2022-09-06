---
layout:     post
title:      【Git】yarn install git 连接失败——解决了 Failed to connect to github.com port 443:connection timed out
subtitle:   【Git】yarn install git 连接失败——解决了 Failed to connect to github.com port 443:connection timed out
date:       2022-07-21 19:06:10
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 后端
    - Mac
    - git
    - github
---

>【Git】yarn install git 连接失败——解决了 Failed to connect to github.com port 443:connection timed out

# 【Git】yarn install git 连接失败——解决了 Failed to connect to github.com port 443:connection timed out


晕了，今天不知道怎么出现了这个问题
端口号看你的代理 git config --global http.proxy http://127.0.0.1:7890   git config --global https.proxy http://127.0.0.1:7890

问题得到解决

![](https://img-blog.csdnimg.cn/573250d1be1242a6a05583ef7b841dcf.png)

取消全局代理
git config --global --unset http.proxy   git config --global --unset https.proxy
