---
layout:     post
title:      【Docker】Docker使用入门指南
subtitle:   【Docker】Docker使用入门指南
date:       2019-05-22 11:09:18
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - docker
---

>【Docker】Docker使用入门指南

# 【Docker】Docker使用入门指南


**目录**

[一、Docker简介](#%E4%B8%80%E3%80%81Docker%E7%AE%80%E4%BB%8B)

[1. Docker的应用场景](#Docker%E7%9A%84%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF)

[2. Docker 的优点](#2.%20Docker%20%E7%9A%84%E4%BC%98%E7%82%B9)

[二、Docker 安装](#%E4%BA%8C%E3%80%81Docker%20%E5%AE%89%E8%A3%85)

[三、Docker简单使用](#%E4%B8%89%E3%80%81Docker%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8)

[四、Docker使用 ](#%E5%9B%9B%E3%80%81Docker%E4%BD%BF%E7%94%A8%C2%A0)

----

## 一、Docker简介

Docker 是一个开源的应用容器引擎，基于 [Go 语言](https://www.runoob.com/go/go-tutorial.html) 并遵从Apache2.0协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

### 1. Docker的应用场景

* Web 应用的自动化打包和发布。
* 自动化测试和持续集成、发布。
* 在服务型环境中部署和调整数据库或其他的后台应用。
* 从头编译或者扩展现有的OpenShift或Cloud Foundry平台来搭建自己的PaaS环境。

### 2. Docker 的优点

* **1、简化程序：**
Docker 让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，便可以实现虚拟化。Docker改变了虚拟化的方式，使开发者可以直接将自己的成果放入Docker中进行管理。方便快捷已经是 Docker的最大优势，过去需要用数天乃至数周的 任务，在Docker容器的处理下，只需要数秒就能完成。
* **2、避免选择恐惧症：**
如果你有选择恐惧症，还是资深患者。Docker 帮你 打包你的纠结！比如 Docker 镜像；Docker 镜像中包含了运行环境和配置，所以 Docker 可以简化部署多种应用实例工作。比如 Web 应用、后台应用、数据库应用、大数据应用比如 Hadoop 集群、消息队列等等都可以打包成一个镜像部署。
* **3、节省开支：**
一方面，云计算时代到来，使开发者不必为了追求效果而配置高额的硬件，Docker 改变了高性能必然高价格的思维定势。Docker 与云的结合，让云空间得到更充分的利用。不仅解决了硬件管理的问题，也改变了虚拟化的方式。

## 二、Docker 安装

安装超级简单，实在不会查看[这个链接](https://www.runoob.com/docker/windows-docker-install.html)。

## 三、Docker简单使用

**1. 运行一个web（nginx）应用**
//安装nginx $ docker pull nginx //运行nginx，本地80端口映射到docker nginx 80端口 $ docker run -d -p 80:80 nginx //访问 http://localhost/ 就可以访问到nginx官方测试页面

**2. 进入docker实例内部的命令为：**

//通过docker ps 获取docker-id $ docker ps //执行进入docker实例内部方法 $ docker exec -it [docker-id] bash

**3. 停止Docker实例的命令：**

//通过docker ps 获取docker-id $ docker ps //执行停止docker实例的命令 $ docker stop [docker-id]

## 四、Docker使用 

**1. 创建一个Java Web项目的镜像**
//1. 在工作目录下创建一个文件 Dockerfile $ vim Dockerfile //2. 安装Java web运行环境 Tomcat $ docker pull Tomcat:latest //3. 编写Dockerfile from tomcat COPY jpress.war /usr/local/tomcat/webapps //4. build 生成一个镜像 $ docker build -t [name] . //-t指定tag名字， . 为目录路径 //5. 最后查看是否生成完毕 $ docker images

**2. 运行一个Java web项目镜像**

//运行jpress 绑定8080到主机的8888端口 $ docker run -d -p 8888:8080 jpress //查看主机8888端口开放状态 $ netstat -na|grep 8888 //此时打开网站就可以访问进入Tomcat http://localhost:8888/jpress/install

**3. 安装MySQL**

//1. 安装mysql $ docker pull mysql:latest //2. 运行mysql $ docker run -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=jpress -d -p 3306:3306 mysql

 


