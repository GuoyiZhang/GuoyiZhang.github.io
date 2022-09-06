---
layout:     post
title:      【Docker】「实战篇」开源项目docker化运维部署-linux和docker基本命令（三）
subtitle:   【Docker】「实战篇」开源项目docker化运维部署-linux和docker基本命令（三）
date:       2019-07-03 17:20:19
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 「实战篇」开源项目docker化运维部署
---

>【Docker】「实战篇」开源项目docker化运维部署-linux和docker基本命令（三）

# 【Docker】「实战篇」开源项目docker化运维部署-linux和docker基本命令（三）

长期使用windows，windows的图形界面非常的方便易用，入门的门槛很低。缺点是图形界面有时候会卡顿，一些软件需要安装完系统需要重新启动，在硬件系统不是很好的情况下，可能会蓝屏死机。这些缺点就阻碍了windows进入服务器市场的主要原因。linux没有这些缺点。

![](//upload-images.jianshu.io/upload_images/11223715-cb9b6b4626dd8d59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/300/format/webp)

### linux

* 系统的优势

1. 跨平台的硬件支持
大到服务器的硬件设备，小到只能手表，只能电视内部都是linux，在看电视的时候非常的流程，不会经常死机。

1. 丰富的软件支持
各种软件很容易很轻松的找的到，比如centos安装软件的时候可以用yum的方式。ubuntu用apt-get。这两个指令安装软件都非常的智能和顺利。

1. linux支持多用户多任务
给不同的用户建立角色，有的角色权利比较大，有的角色权限比较小，才相对的来说比较安全。

1. 可靠的安全性
病毒最多的是windows，病毒相对比较少的mac os，linux系统，主要mac os和linus系统他们的权限比较健全。就算病毒放到了linux系统，但是他没有权限也无法启动。

1. 良好的稳定性
windows系统安全一些关键应用的时候，需要提示重启才生效。感受特别不好，linux号称20年不重启，不死机。

1. 完善的网络功能
linux的网络防火墙完善，自身的防火墙已经很强大的。

* 目录结构

![](//upload-images.jianshu.io/upload_images/11223715-eb8e738ff5dfa629.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 命令介绍<其实我都懒的总结啊，照顾新入门的老铁啊>
列出目录内容
 
ls
 
创建目录
 
mkdir
 
创建文件
 
touch file.txt echo idig8.com>file.txt cat file.txt
 
复制文件或者目录 ，-r是目录
 
cp myfile newfile cp -r myfile newfile
 
删除文件或者目录，-r目录，-f不需要提示y/n <谨慎使用>
 
rm -rf myfile
 
更改权限
 
chmod 700 newfile

* linux7 防火墙
centos7默认安装的firewalld防火墙，可以控制来自互联网的访问限制传输数据的通过。

![](//upload-images.jianshu.io/upload_images/11223715-6e9f94038e7d7318.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

/#状态 firewall-cmd --state /#启动 service firewall start /#关闭 service firewall stop /#重启 service firewall restart /#添加端口段 firewall-cmd --permanent --add-port=8080-8085/tcp /#端口生效 firewall-cmd --reload /#删除端口段 firewall-cmd --permanent --remove-port=8080-8085/tcp /#查看开启的端口 firewall-cmd --permanent --list-ports /#查看开启的服务 firewall-cmd --permanent --list-services

docker

直接在linux上安装应用不完了，为啥要搞这么复杂非的搞个容器化，其实就是为了解决隔离性的问题，使用虚拟机部署环境比较方便。如果直接在linux之内，可能我把A程序卸载，直接影响到了B程序因为他们有相互关联的软件包。vmware 属于重量级虚拟机，docker是轻量级虚拟机。

* docker虚拟机和云计算的关系
想把自己的项目部署到服务器上，我们在本地真实的搭建服务器成本很高的，固定的ip，服务器硬件，宽带申请等等吧反正是不划算。经常做的事情到云空间申请个虚拟的空间，一般在云空间厂家哪里购买几核cpu，多大内存的机器付好款就归你使用。其实这种方式用docker也是可以实现的，因为本身docker的空间就是容器，docker虚拟机在创建容器的时候，可以设置这个虚拟空间创建多大的内存，cpu是什么样的配置，网络使用是什么样子的，这其实就是aas云。申请完虚拟云之后，操作系统都是白的里面什么都没安装，那就比较麻烦需要安装需要的一些软件，后来厂家又想起来一些预装功能，nginx和redis 自己需要的一些软件。其实这就是paas平台。但是有的用户说你给我安装好mysql，tomcat，各种软件，但是我没有开发能力，我就给你oa，erp项目，里面有现成的oa和erp系统。直接用就好了。这就是saas平台。

![](//upload-images.jianshu.io/upload_images/11223715-dcae2c11c1cb1f06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

镜像是用来创建容器的。容器是从镜像里面创建的实例

![](//upload-images.jianshu.io/upload_images/11223715-b7a4fb26ea691c25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

安装docker
 
yum -y update yum install -y docker
 
docker启动和关闭，重启
 
serivce docker start service docker stop service docker restart

![](//upload-images.jianshu.io/upload_images/11223715-65ebd9a23c9d1206.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

搜索安装镜像，国内拉取镜像比较慢，建议使用DaoCloud
 
docker search java docker pull java
 
导出导入镜像
 
/#导出 docker save java>/home/java.tar.gz /#导入 docker load</home/java.tar.gz
 
启动镜像会创建一个运行状态的容器
 
docker run -d -it --name java java bash
 
暂停和停止容器
 
docker pause 容器名称 docker unpause 容器名称 docker stop 容器ID docker start 容器ID

PS：这都很初级的，其实就是让大家回顾下，下一步就是为了更好的部署项目。

作者：IT人故事会
链接：https://www.jianshu.com/p/3f4060533387
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

