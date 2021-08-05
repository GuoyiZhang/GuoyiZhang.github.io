---
layout:     post
title:      【Docker】「实战篇」开源项目docker化运维部署-借助dockerSwarm搭建集群部署（九）
subtitle:   【Docker】「实战篇」开源项目docker化运维部署-借助dockerSwarm搭建集群部署（九）
date:       2019-07-03 17:54:32
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 开发工具
    - 「实战篇」开源项目docker化运维部署
---

>【Docker】「实战篇」开源项目docker化运维部署-借助dockerSwarm搭建集群部署（九）

# 【Docker】「实战篇」开源项目docker化运维部署-借助dockerSwarm搭建集群部署（九）

为了让学习的知识融汇贯通，目前是把所有的集群都放在了一个虚拟机上，如果这个虚拟机宕机了怎么办？俗话说鸡蛋不要都放在一个篮子里面，把各种集群的节点拆分部署，应该把各种节点分机器部署，多个宿主机，这样部署随便挂哪个主机我们都不担心。
源码：[https://github.com/limingios/netFuture/blob/master/docker-swarm/](https://github.com/limingios/netFuture/blob/master/docker-swarm/)

![](//upload-images.jianshu.io/upload_images/11223715-346c707f7e62ea45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

一起在说说docker swarm
swarm 是docker的三剑客一员，之前都说过了，可以看中级和高级啊 。

1. docker machine 容器服务
1. docker compose 脚本服务
1. docker swarm 容器集群技术

![](//upload-images.jianshu.io/upload_images/11223715-328bd466ac6c6ce4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 去中心化的设计
Swarm Manager 也承担worker节点的作用。
Swarm Worker 运行容器部署项目

![](//upload-images.jianshu.io/upload_images/11223715-6e5a59cd84af7e7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

Swarm是没有中心节点的，挂到其中一个其他是不会挂掉的。Swarm Manager 如果master挂了，立马选举一个新的master。

* 创建集群环境
首先机器已经安装了docker环境。
 
docker swarm init

* 加入swarm集群
/#加入到manager中 docker swarm join-token manager /#加入到worker中 docker swarm join-token worker

环境搭建

应用IP地址服务配置安装应用安装方式docker-swarm-manager1192.168.66.100docker-swarm-manager1单核 2g内存docker-swarm-manager1dockerdocker-swarm-manager2192.168.66.101docker-swarm-manager2单核 2g内存docker-swarm-manager2dockerdocker-swarm-node1192.168.66.102docker-swarm-node1单核 2g内存docker-swarm-node1dockerdocker-swarm-node2192.168.66.103docker-swarm-node2单核 2g内存docker-swarm-node2docker

![](//upload-images.jianshu.io/upload_images/11223715-fb0fd04a68917b0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

docker swarm环境
一共4个节点，2个manager节点，2个work节点，manager不光是管理，而且也干活，说白了一共4个干活的节点。
 
创建docker swarm集群
 
su - /#密码vagrant docker swarm init
 
报错注意：如果你在新建集群时遇到双网卡情况，可以指定使用哪个 IP，例如上面的例子会有可能遇到下面的错误。
 
Error response from daemon: could not choose an IP address to advertise since this system has multiple addresses on different interfaces (10.0.2.15 on enp0s3 and 192.168.66.100 on enp0s8) - specify one with --advertise-addr
 
再次创建docker swarm集群192.168.66.100
 
docker swarm init --advertise-addr 192.168.66.100 --listen-addr 192.168.66.100:2377 docker swarm join-token manager

![](//upload-images.jianshu.io/upload_images/11223715-dac8661aa1ce69f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

 

![](//upload-images.jianshu.io/upload_images/11223715-ec43f67b7653c466.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
再次创建docker swarm集群192.168.66.101
当前节点以manager的身份加入swarm集群
 
docker swarm join --token SWMTKN-1-4itumtscktomolcau8a8cte98erjn2420fy2oyj18ujuvxkkzx-9qutkvpzk87chtr4pv8770mcb 192.168.66.100:2377

![](//upload-images.jianshu.io/upload_images/11223715-1b6d8130d322a35a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

再次创建docker swarm集群192.168.66.102
当前节点以worker的身份加入swarm集群
 
docker swarm join --token SWMTKN-1-4itumtscktomolcau8a8cte98erjn2420fy2oyj18ujuvxkkzx-f2dlt8g3hg86gyc9x6esewtwl 192.168.66.100:2377

![](//upload-images.jianshu.io/upload_images/11223715-fc338ca77838a71e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

再次创建docker swarm集群192.168.66.103
当前节点以worker的身份加入swarm集群
 
docker swarm join --token SWMTKN-1-4itumtscktomolcau8a8cte98erjn2420fy2oyj18ujuvxkkzx-f2dlt8g3hg86gyc9x6esewtwl 192.168.66.100:2377

![](//upload-images.jianshu.io/upload_images/11223715-176a8a5ba382d4e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

查看swarm集群
只能在manager节点内执行
leader挂掉后，reachable就可以管理集群了。
 
docker node ls

![](//upload-images.jianshu.io/upload_images/11223715-172c6e71cd84d0f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

查看swarm集群的网络
只能在manager节点内执行
 
docker network ls

![](//upload-images.jianshu.io/upload_images/11223715-7c16cf77c78d9475.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

创建容器间的共享网络
只能在manager节点内执行
 
docker network create -d overlay --attachable swarm_test docker network ls

![](//upload-images.jianshu.io/upload_images/11223715-e98b92cabdfb8f6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

目前是4台机器，如果想让4台机器内的容器可以进行共享，overlay的网络就可以了，只需要在创建容器的时候--net=swarm_test

![](//upload-images.jianshu.io/upload_images/11223715-d0b1e0134f757b68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

创建5个pxc容器

* 192.168.66.100
创建2个容器。之前也说过如何创建，最为重要的是共享网络一定要使用swarm的共享网络。
 
docker volume create v1 docker volume create backup1 /#增加域名解析 echo "nameserver 8.8.8.8" | tee /etc/resolv.conf > /dev/null sudo curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://b81aace9.m.daocloud.io sudo systemctl restart docker docker run -d -p 3306:3306 --net=swarm_test \ --name=node1 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -v v1:/var/lib/mysql \ --privileged \ -v backup1:/data \ percona/percona-xtradb-cluster docker ps

![](//upload-images.jianshu.io/upload_images/11223715-794c499d419c0b17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-760a340f61145784.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-31ef393977332b53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
docker volume create v2 docker run -d -p 3307:3306 --net=swarm_test \ --name=node2 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -v v2:/var/lib/mysql \ --privileged \ -v backup1:/data \ -e CLUSTER_JOIN=node1 \ percona/percona-xtradb-cluster docker ps

![](//upload-images.jianshu.io/upload_images/11223715-399542ca1a075949.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 192.168.66.101
创建1个容器。之前也说过如何创建，最为重要的是共享网络一定要使用swarm的共享网络。
 
docker volume create v3 docker volume create backup3 docker run -d -p 3307:3306 --net=swarm_test \ --name=node3 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -v v3:/var/lib/mysql \ --privileged \ -v backup3:/data \ -e CLUSTER_JOIN=node1 \ percona/percona-xtradb-cluster docker ps

![](//upload-images.jianshu.io/upload_images/11223715-23521a0213181ac1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 192.168.66.102
创建1个容器。之前也说过如何创建，最为重要的是共享网络一定要使用swarm的共享网络。
 
docker volume create v4 docker volume create backup4 docker run -d -p 3307:3306 --net=swarm_test \ --name=node4 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -v v4:/var/lib/mysql \ --privileged \ -v backup4:/data \ -e CLUSTER_JOIN=node1 \ percona/percona-xtradb-cluster docker ps

* 192.168.66.103
创建1个容器。之前也说过如何创建，最为重要的是共享网络一定要使用swarm的共享网络。
 
docker volume create v4 docker volume create backup4 docker run -d -p 3307:3306 --net=swarm_test \ --name=node4 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -v v4:/var/lib/mysql \ --privileged \ -v backup4:/data \ -e CLUSTER_JOIN=node1 \ percona/percona-xtradb-cluster docker ps

![](//upload-images.jianshu.io/upload_images/11223715-fdc6003849a2e5d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-711cb9704f30de8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

容器集群
在这个示意图里面，画了4个linux的主机，都安装了docker虚拟机，假如我现在想安装A程序，直接选择Docker-1这个主机里面的容器安装A程序，这样没有问题。但是单节点单容器来部署，一旦这个节点挂掉的话，A程序就没有，为了防止这样我们有冗余设计，直接在Docker-2这个主机里面的容器也安装A程序，这样的话，Docker-1里面的A程序挂了，Docker-2里面的A程序也可以运行。这个功能有点像负载均衡，其实真的很像。docker swarm提供的东西跟负载均衡还是有区别的。swarm 只是提供了容器状态的管理，如果Docker-1里面的A程序挂了，发现本来二个，现在变成1个了危险，立马在起一个吧。实时保证docker容器内的数量。

![](//upload-images.jianshu.io/upload_images/11223715-7d348ca7c904fce2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-036a16812a7e5533.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

容器集群适合的场景
容器集群不适合有状态程序，例如数据库，缓存等等

![](//upload-images.jianshu.io/upload_images/11223715-d100983ba12e105f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

 

 

退出Swarm集群
/#Manager退出必须加--force docker swarm leave --force

删除节点

service docker stop /#Manager节点需要先降级 docker node demoted <nodeID> docker node rm <nodeID> docker network

PS：这次主要说了3点比较重要的：通过docker-swarm的网络搭建容器化通信网络，创建集群的时候--net 加入docker-swarm创建的网络，方便通信。不哪些适合做容器化集群，数据库和缓存。如何操作docker-swarm中的节点。

作者：IT人故事会
链接：https://www.jianshu.com/p/1691dce8684d
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

