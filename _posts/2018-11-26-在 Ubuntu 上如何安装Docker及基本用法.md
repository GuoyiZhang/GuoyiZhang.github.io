---
layout:     post
title:      在 Ubuntu 上如何安装Docker及基本用法
subtitle:   在 Ubuntu 上如何安装Docker及基本用法
date:       2018-11-26 18:43:07
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Linux
    - EOS
    - Docker
    - Ubuntu
---

>在 Ubuntu 上如何安装Docker及基本用法

# 在 Ubuntu 上如何安装Docker及基本用法


# [在 Ubuntu 18.04 上安装 Docker](https://blog.csdn.net/fei2636/article/details/79384969%C2%A0)

以下我们将指导你如何安装 docker。在安装之前我们需要检查 kernel 版本和操作系统架构。

1. 运行命令：
root@ubuntu:~/# uname -a Linux ubuntu 4.15.0-39-generic /#42-Ubuntu SMP Tue Oct 23 15:48:01 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux

 

2. 现在运行安装 Docker 的命令：
sudo apt-get install -y docker.io

等待安装完毕，现在我们使用下面的命令启动 Docker：

systemctl start docker

运行系统引导时启用 docker，命令：

systemctl enable docker

你可能想核对一下 docker 版本：

docker version

现在，docker 已经安装在您的系统上。您可以从 Docker 库先下载 Docker Image 制作的容器。
https://blog.csdn.net/fei2636/article/details/79384969 
 


