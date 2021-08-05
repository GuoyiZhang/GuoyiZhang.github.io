---
layout:     post
title:      【Docker】「实战篇」开源项目docker化运维部署-mysql 热备份数据（五）
subtitle:   【Docker】「实战篇」开源项目docker化运维部署-mysql 热备份数据（五）
date:       2019-07-03 17:28:20
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 开发工具
    - 「实战篇」开源项目docker化运维部署
---
>blog.getTitle() 

# 【Docker】「实战篇」开源项目docker化运维部署-mysql 热备份数据（五）

本次说说热热备份数据，上次搭建了pxc的集群，搭建好了复杂均衡，做了双机热备这种方案。无论做前后端分离的项目，还是做微服务的项目，都需要有一个强大稳定的集群。数据库备份分为：热备份和冷备份，如果项目没有上线冷备份没问题。如果上线用冷备份就有问题。源码：[https://github.com/limingios/netFuture/tree/master/mysql-pxc/](https://github.com/limingios/netFuture/tree/master/mysql-pxc/)

![](//upload-images.jianshu.io/upload_images/11223715-822a1aeefea805e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/877/format/webp)

冷备份

1. 冷备份是关闭数据库时候的备份方式，通常做法是拷贝数据文件
1. 冷备份是最简单最安全的一种备份方式
1. 大型网站无法做到关闭业务备份数据，所以冷备份不是最佳选择

* PXC冷备份方案
先让其中的一个PXC下线，然后通过拷贝数据文件的方式完成备份，备份完毕下线的PXC上线，完整于其他节点的自动同步。

![](//upload-images.jianshu.io/upload_images/11223715-b193d6380a87e58f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/672/format/webp)

热备份

1. 热备份是在系统运行的状态下备份数据，也是难度最大的备份。举个例子，如果淘宝下线1个小时备份数据，淘宝损失多少钱，谁受的的了啊。这都是白花花的银子啊。
1. Mysql常用的热备份有LVM和XtraBackup两种方案。LVM是针对的分区备份，针对linux系统下的，他号称任何一种数据库都可以完成备份，但是有个弊端，就是在备份的时候只能读不能写入。
1. 建议使用XtrBackup热备Mysql，不需要锁，备份的时候即可读也可以写，XtraBackup而且还是免费的。

XtrBackup
是一款基于InnoDB的在线热备工具，具有开源免费的，支持在线热备，占用磁盘空间小，能够非常快速的备份与恢复mysql数据库。它支持mysql的各种衍生版本。

![](//upload-images.jianshu.io/upload_images/11223715-fd6725c71436a0a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/572/format/webp)

* XtraBackup优势

1. 备份过程不锁表，快速可靠
1. 备份过程不会打断正在执行的事务
1. 能够基于压缩等功能节约磁盘空间和流量

* 全量备份和增量备份

1. 全量备份是备份全部数据，备份过程时间，占用空间大。
1. 增量备份是只备份变化的那部分数据。备份时间短。占用空间小。
在正常的生产系统上，一般是一周做一次全量的备份，一周做一次增量的备份。就足够了。

XtraBackup 安装

* 准备工作
这个工具要求在数据库的节点之内。备份的出来的数据就直接。需要创建一个数据卷，他用来备份XtraBackup 产生的文件，然后映射到宿主机的磁盘里面。
 
创建数据卷
 
docker volume create backup

![](//upload-images.jianshu.io/upload_images/11223715-7b40c90f9748b8ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/409/format/webp)

停止其中一个节点，这里选择node1，目的就是为了删除node1，增加新创建的数据卷。已经运行的容器是不可以增加新的数据卷的。只要还挂载v1的数据卷，v1的文件并没有删除，所以数据不会丢失。
 
docker stop node1 docker rm node1

![](//upload-images.jianshu.io/upload_images/11223715-e8930413b0d76aa0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/334/format/webp)

重新安装node1 挂载新的节点，并同步其他4个节点。 --CLUSTER_JONIN=node2 新创建的node1，同步node2节点。
 
docker run -d -p 3306:3306 --net=net1 --name=node1 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -v v1:/var/lib/mysql \ --privileged \ --ip 172.18.0.2 \ -v backup:/data \ -e CLUSTER_JOIN=node2 \ percona/percona-xtradb-cluster

![](//upload-images.jianshu.io/upload_images/11223715-d651454e78a6e5c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/624/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-c0dc8f30c1a94708.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* PXC 全量备份数据
PXC容器中安全XtraBackup，并执行备份，后悔啊当初PXC的时候没直接找个带XtraBackup的镜像。
 
docker exec -it --user root node1 echo "nameserver 8.8.8.8" | tee /etc/resolv.conf > /dev/null apt-get clean apt-get update apt-get install vim vi /etc/apt/sources.list

![](//upload-images.jianshu.io/upload_images/11223715-006451ae7efbdf39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/812/format/webp)

sources.list 添加下面的内容
 
deb http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb-src http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted

![](//upload-images.jianshu.io/upload_images/11223715-681b00d097083d25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

apt-get clean apt-get update apt-get install percona-xtrabackup-24

![](//upload-images.jianshu.io/upload_images/11223715-cc286da8a00e7599.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/835/format/webp)

全量备份的命令
出现completed OK！说明备份完毕。
 
innobackupex --user=root --password=a123456 /data/backup/full

![](//upload-images.jianshu.io/upload_images/11223715-8cadd002e2ea8db4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

PXC 全量恢复步骤

1. 
数据库可以热备份，但是不能热还原。为了避免恢复过程中的数据同步，我们采用空白的mysql还原数据，然后再建立PXC集群的方式。所以在开发中一定要注意权限问题，不要给开发人员root用户。
1. 
还原数据前要将未提交的事务回滚，还原数据之后重启!

* 执行代码
原来的容器全部删除
 
docker stop node1 node2 node3 node4 node5 docker rm node1 node2 node3 node4 node5 docker volume rm v1 v2 v3 v4 v5 docker volume create v1

![](//upload-images.jianshu.io/upload_images/11223715-f539f88d56adc8aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/859/format/webp)

创建node1 和数据卷
 
docker volume create v1 docker run -d -p 3306:3306 --net=net1 --name=node1 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -v v1:/var/lib/mysql \ --privileged \ --ip 172.18.0.2 \ -v backup:/data \ percona/percona-xtradb-cluster

![](//upload-images.jianshu.io/upload_images/11223715-c7fabf757167b4c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

进入容器内还原数据库
 
/#root用户登录 docker exec -it --user root node1 bash /#删除数据 rm -rf /var/lib/mysql//* /#没有提交的数据回滚 innobackupex --user=root --password=a123456 --apply-back /data/backup/full/2018-12-06_17-18-19/ /#执行下冷还原 innobackupex --user=root --password=a123456 --copy-back /data/backup/full/2018-12-06_17-18-19/ chown -R mysql:mysql /var/lib/mysql/

![](//upload-images.jianshu.io/upload_images/11223715-d4aedab9d8126d55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-1c350f1d54aeb629.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
退出容器重启下，node1节点
 
docker stop node1 docker start node1

PS：数据库的热备份，冷还原也讲完了，真心感觉也不是那么复杂。其实就是这样，但是在云平台越来越盛行的今天，基本上买个rdrs数据库这些功能都有了。了解下XtraBackup 这个工具确定很重要晚上很多的写成shell脚本的，更加方便了。

作者：IT人故事会
链接：https://www.jianshu.com/p/b55c371b73b9
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

