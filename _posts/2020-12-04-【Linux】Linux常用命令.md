---
layout:     post
title:      【Linux】Linux常用命令
subtitle:   【Linux】Linux常用命令
date:       2020-12-04 15:56:27
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
---

>【Linux】Linux常用命令

# 【Linux】Linux常用命令


# **目录**

[一，安装](#1.%20yum%E5%AE%89%E8%A3%85)

[1. yum安装](#1.%20yum%E5%AE%89%E8%A3%85)

[2. Linux安装vim编辑器](#2.%C2%A0Linux%E5%AE%89%E8%A3%85vim%E7%BC%96%E8%BE%91%E5%99%A8)

[ 3. Linux安装ShadowSocks](#%C2%A03.%C2%A0Linux%E5%AE%89%E8%A3%85ShadowSocks)

[ 4. ubuntu找不到ifconfig](#%C2%A04.%20ubuntu%E6%89%BE%E4%B8%8D%E5%88%B0ifconfig)

[ 5.](#%C2%A05.%20curl%20%E5%AE%89%E8%A3%85)curl[安装](#%C2%A05.%20curl%20%E5%AE%89%E8%A3%85)

[6. webStrom安装与破解](#6.%20webStrom%20%E5%AE%89%E8%A3%85%E4%B8%8E%E7%A0%B4%E8%A7%A3)

[4.Ubuntu18.04（GNOME桌面）主题美化，苹果机的私人定制](#4.Ubuntu18.04%EF%BC%88GNOME%E6%A1%8C%E9%9D%A2%EF%BC%89%E4%B8%BB%E9%A2%98%E7%BE%8E%E5%8C%96%EF%BC%8C%E8%8B%B9%E6%9E%9C%E6%9C%BA%E7%9A%84%E7%A7%81%E4%BA%BA%E5%AE%9A%E5%88%B6)

[4.Linux也可以这样美--Ubuntu18.04安装，配置，美化 - 踩坑记](#4.Linux%E4%B9%9F%E5%8F%AF%E4%BB%A5%E8%BF%99%E6%A0%B7%E7%BE%8E--Ubuntu18.04%E5%AE%89%E8%A3%85%EF%BC%8C%E9%85%8D%E7%BD%AE%EF%BC%8C%E7%BE%8E%E5%8C%96%20-%20%E8%B8%A9%E5%9D%91%E8%AE%B0)

----

# 一，安装

## 1. yum安装

Linux的：
apt install yum

## 2. [Linux安装vim编辑器](https://www.cnblogs.com/raorao1994/p/8890751.html)

1，ubuntu的的的系统：普通用户下输入命令：
sudo apt-get install vim-gtk

（注：出现E：无法找到包则将命令改成sudo apt-get install vim-nox）

2，ubuntu的的的系统：普通用户下输入命令：
yum -y install vim/*

##  3. Linux安装ShadowSocks

##  4. ubuntu找不到ifconfig

打开终端运行  
apt-get install net-tools -y

##  5.curl安装

apt-get install curl -y

## 6. webStrom安装与破解

WebStorm作为一款比较火的前段开发工具，确实是很优秀，支持Windows，MacOS，Linux接下来就是教大家如何安装并激活，网上有很多激活码，但是很多都是现实无效或者过期了，话不多说，上方法！

首先自己百度在Ubuntu的系统下安装JDK，这个很简单。

首先去官网下载WebStorm，下载最新版本的就可以，（强大的Webstorm IDE也可以开发TypeScript，还支持自动编译成js文件。），

进入到/下载目录手动提取出来就可以了，然后将目录移动到home下的/ opt文件夹中
sudo mv WebStorm-182.3911.37/ /opt/

然后再进入到目录下的/ bin文件夹下，

cd /opt/WebStorm-182.3911.37/bin

然后运行webstorm

sh webstorm.sh 

这时候会提示你进行验证激活，先不要动。

先进入到自己的主机文件夹将下边的这句放入到里边进行保存
sudo vim /etc/hosts

＃打开hosts文件，然后将“0.0.0.0 account.jetbrains.com”（不要复制引号）放入到最上边保存即可即可
然后进入到这里 - “http://idea.lanyus.com/，点击获得注册码然后将弹出的注册码复制到刚打开的Webstorm激活界面，点击激活就可以了，确完事。

# 7. apt-get

[https://blog.csdn.net/flydream0/article/details/8620396](https://blog.csdn.net/flydream0/article/details/8620396)

# 4.Ubuntu18.04（GNOME桌面）主题美化，苹果机的私人定制

[https://blog.csdn.net/zyqblog/article/details/80152016](https://blog.csdn.net/zyqblog/article/details/80152016)

 

# 4.Linux也可以这样美--Ubuntu18.04安装，配置，美化 - 踩坑记

[https://blog.csdn.net/github_36498175/article/details/80161362](https://blog.csdn.net/github_36498175/article/details/80161362)

 

