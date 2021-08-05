---
layout:     post
title:      【Docker】「实战篇」开源项目docker化运维部署-搭建mysql集群（四）
subtitle:   【Docker】「实战篇」开源项目docker化运维部署-搭建mysql集群（四）
date:       2019-07-03 17:24:23
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 开发工具
    - 「实战篇」开源项目docker化运维部署
---
>blog.getTitle() 

# 【Docker】「实战篇」开源项目docker化运维部署-搭建mysql集群（四）

有了docker虚拟机，就需要利用平台部署数据库的集群，在实际操作之前介绍下数据库集群的方案和各自的特点。源码：[https://github.com/limingios/netFuture/tree/master/mysql-pxc/](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Flimingios%2FnetFuture%2Ftree%2Fmaster%2Fmysql-pxc%2F)

![](//upload-images.jianshu.io/upload_images/11223715-fd1baeb357650060.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/634/format/webp)

### 集群的方案

单节点的弊病

1. 大型互联网程序用户群体庞大，所以架构必须要特殊设计
1. 单节点的数据库无法满足性能的要求
案例15年前，高考成绩可以在网上查（河南几十万考生），那时候基本家里都没电脑，都是去网吧白天去网吧也查不了，太慢了，后来半夜通宵有时候可以查到，也就是说白天基本查不了人太多了。晚上看运气。一个数据库的实例1万多人就无法反应了。

1. 单节点的数据库没有冗余设计，无法满足高可用

常用的mysql集群设计方案

* Replication

1. 速度快
1. 弱一致性
1. 低价值
1. 场景：日志，新闻，帖子

* PXC

1. 速度慢
1. 强一致性
1. 高价值
1. 场景：订单，账户，财务
Percona Xtradb Cluster，简称PXC。是基于Galera插件的MySQL集群。相比那些比较传统的基于主从复制模式的集群架构MHA和MM+keepalived，galera cluster最突出特点就是解决了诟病已久的数据复制延迟问题，基本上可以达到实时同步。而且节点与节点之间，他们相互的关系是对等的。本身galera cluster也是一种多主架构。galera cluster最关注的是数据的一致性，对待事物的行为时，要么在所有节点上执行，要么都不执行，它的实现机制决定了它对待一致性的行为非常严格，这也能非常完美的保证MySQL集群的数据一致性。在PXC里面任何一个节点都是可读可写的。在其他的节点一定是可以读取到这个数据。

* 建议PXC使用PerconaServer（MYSQL的改进版，性能提升很大）

* PXC方案和Replication方案的对比
PXC任意一个节点都可以存在读写的方案，也就是任意一个节点都可以当读或者当写。同步复制。保证强一致性。

![](//upload-images.jianshu.io/upload_images/11223715-c58d433dbf20538b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

Replication方案，主从的方式，他是采用异步的方式。

![](//upload-images.jianshu.io/upload_images/11223715-9b6f6bd6f088dba2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/803/format/webp)

* PXC的数据强一致性
同步复制，事务在所有节点要提交都提交。要么都不提交

![](//upload-images.jianshu.io/upload_images/11223715-4a644fabcfff0c6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* Replication弱一致性，主要master成功就成功了。返回给调用者。如果slave失败，也没办法。因为已经告诉调用者成功了，调用者获取查询的时候查询不到信息。例如：在淘宝买个东西，付款也成功了，查询订单没信息是不是要骂娘。

![](//upload-images.jianshu.io/upload_images/11223715-fbd7b2c47fe1d519.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

### 环境搭建

应用IP地址服务配置安装应用安装方式docker-mysql192.168.66.100docker-mysql双核 8g内存docker-mysqldocker

(1). 虚拟机vagrant讲述安装的步骤

vagrant up

![](//upload-images.jianshu.io/upload_images/11223715-f885c8dca5649b0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

(2).机器window/mac开通远程登录root用户下
su - /# 密码 vagrant /#设置 PasswordAuthentication yes vi /etc/ssh/sshd_config sudo systemctl restart sshd

PXC集群安装介绍

PXC既可以在linux系统安装，也可以在docker上面安装。

* 安装镜像PXC镜像
docker pull percona/percona-xtradb-cluster /#本地安装 docker load </home/soft/pxc.tar.gz

* 创建内部网络的
处于安全，需要给PXC集群实例创建Docker内部网络，都出可虚拟机自带的网段是172.17.0.*， 这是内置的一个网段，当你创建了网络后，网段就更改为172.18.0.*，
 
docker network create net1 docker network inspect net1 docker network rm net1 /#设置网段 docker network create --subnet=172.18.0.0/24 net1

![](//upload-images.jianshu.io/upload_images/11223715-faaa49bf1b8a138a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 创建Docker 卷
一旦生成docker容器，不要在容器内保存业务的数据，要把数据放到宿主机上，可以把宿主机的一个目录映射到容器内，如果容器出现问题，只需要吧容器删除，重新建立一个新的容器把目录映射给新的容器。
 
之前一直有个疑问，如果直接映射目录的吧，存在失败的问题，现在终于知道解决方案了，直接映射docker卷就可以可以忽略这个问题了。
 
容器中的PXC节点映射数据目录的解决方法
 
docker volume create name --v1

mysql pxc搭建

* 脚本开发
/#!/bin/bash echo "创建网络" docker network create --subnet=172.18.0.0/24 net1 echo "创建5个docker卷" docker volume create v1 docker volume create v2 docker volume create v3 docker volume create v4 docker volume create v5 echo "创建节点 node1" docker run -d -p 3306:3306 --net=net1 --name=node1 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -v v1:/var/lib/mysql \ --privileged \ --ip 172.18.0.2 \ percona/percona-xtradb-cluster sleep 1m echo "创建节点 node2" docker run -d -p 3307:3306 --net=net1 --name=node2 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -e CLUSTER_JOIN=node1 \ -v v2:/var/lib/mysql \ --privileged \ --ip 172.18.0.3 \ percona/percona-xtradb-cluster sleep 1m echo "创建节点 node3" docker run -d -p 3308:3306 --net=net1 --name=node3 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -e CLUSTER_JOIN=node1 \ -v v3:/var/lib/mysql \ --privileged \ --ip 172.18.0.4 \ percona/percona-xtradb-cluster sleep 1m echo "创建节点 node4" docker run -d -p 3309:3306 --net=net1 --name=node4 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -e CLUSTER_JOIN=node1 \ -v v4:/var/lib/mysql \ --privileged \ --ip 172.18.0.5 \ percona/percona-xtradb-cluster sleep 1m echo "创建节点 node5" docker run -d -p 3310:3306 --net=net1 --name=node5 \ -e CLUSTER_NAME=PXC \ -e MYSQL_ROOT_PASSWORD=a123456 \ -e XTRABACKUP_PASSWORD=a123456 \ -e CLUSTER_JOIN=node1 \ -v v5:/var/lib/mysql \ --privileged \ --ip 172.18.0.6 \ percona/percona-xtradb-cluster

![](//upload-images.jianshu.io/upload_images/11223715-ffec49643614d952.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-bc72b264142a0855.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/927/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-10a6a613e766f48e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/365/format/webp)
新建立一个aaa数据库 结果都可以用

![](//upload-images.jianshu.io/upload_images/11223715-d8d6d8078dd943d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/550/format/webp)

哇塞就这么简单，成功的搭建了mysql的集群

增加负载均衡方案

目前数据库都是独立的ip，在开发的时候总不能随机连接一个数据库吧。如果想请求，统一的口径，这就需要搭建负载均衡了。虽然上边已经搭建了集群，但是不使用数据库负载均衡，单节点处理所有请求，负载高，性能差。下图就是一个节点很忙，其他节点很闲。

![](//upload-images.jianshu.io/upload_images/11223715-00b7e9c33213798d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 调整后的方案，使用Haproxy做负载均衡，请求被均匀分发到每个节点，单节点的负载低，性能好。haproxy不是数据库，只是一个转发器。

![](//upload-images.jianshu.io/upload_images/11223715-6265d79ef6d16079.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 负载均衡中间件对比
LVS是不能在虚拟机环境下安装的。

![](//upload-images.jianshu.io/upload_images/11223715-7d3a4e64b5197085.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 安装haproxy
docker-haproxy的介绍：[https://hub.docker.com/_/haproxy/](https://links.jianshu.com/go?to=https%3A%2F%2Fhub.docker.com%2F_%2Fhaproxy%2F)

![](//upload-images.jianshu.io/upload_images/11223715-4b05764c0f701faa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 配置文件
mkdir haproxy/h1 pwd vi haproxy.cfg

![](//upload-images.jianshu.io/upload_images/11223715-eda65af526f8464b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/811/format/webp)

* haproxy.cfg配置
登录：admin
密码：abc123456
 
global /#工作目录 chroot /usr/local/etc/haproxy /#日志文件，使用rsyslog服务中local5日志设备（/var/log/local5），等级info log 127.0.0.1 local5 info /#守护进程运行 daemon defaults log global mode http /#日志格式 option httplog /#日志中不记录负载均衡的心跳检测记录 option dontlognull /#连接超时（毫秒） timeout connect 5000 /#客户端超时（毫秒） timeout client 50000 /#服务器超时（毫秒） timeout server 50000 /#监控界面 listen admin_stats /#监控界面的访问的IP和端口 bind 0.0.0.0:8888 /#访问协议 mode http /#URI相对地址 stats uri /dbs /#统计报告格式 stats realm Global\ statistics /#登陆帐户信息 stats auth admin:abc123456 /#数据库负载均衡 listen proxy-mysql /#访问的IP和端口 bind 0.0.0.0:3306 /#网络协议 mode tcp /#负载均衡算法（轮询算法） /#轮询算法：roundrobin /#权重算法：static-rr /#最少连接算法：leastconn /#请求源IP算法：source balance roundrobin /#日志格式 option tcplog /#在MySQL中创建一个没有权限的haproxy用户，密码为空。Haproxy使用这个账户对MySQL数据库心跳检测 option mysql-check user haproxy server MySQL_1 172.18.0.2:3306 check weight 1 maxconn 2000 server MySQL_2 172.18.0.3:3306 check weight 1 maxconn 2000 server MySQL_3 172.18.0.4:3306 check weight 1 maxconn 2000 server MySQL_4 172.18.0.5:3306 check weight 1 maxconn 2000 server MySQL_5 172.18.0.6:3306 check weight 1 maxconn 2000 /#使用keepalive检测死链 option tcpka

![](//upload-images.jianshu.io/upload_images/11223715-463321f7ff5dd040.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 创建docker下的haproxy容器
docker run -it -d -p 4001:8888 \ -p 4002:3306 \ -v /root/haproxy/h1:/usr/local/etc/haproxy \ --name h1 --privileged --net=net1 \ --ip 172.18.0.7 haproxy

![](//upload-images.jianshu.io/upload_images/11223715-668165b2f3b1071c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 进入容器后，加载配置文件
docker exec -it h1 /bin/bash haproxy -f /usr/local/etc/haproxy/haproxy.cfg

![](//upload-images.jianshu.io/upload_images/11223715-6686f5fcbffb7a3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 在数据库中创建一个haproxy的用户，不需要设置密码

![](//upload-images.jianshu.io/upload_images/11223715-06072dc10019f585.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 登录haproxy网页端
[http://192.168.66.100:4001/dbs](https://links.jianshu.com/go?to=http%3A%2F%2F192.168.66.100%3A4001%2Fdbs)

![](//upload-images.jianshu.io/upload_images/11223715-d3928f2ec3b2fc1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-e8b353c7d2a4cfaa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 客户端连接haproxy-mysql数据库

![](//upload-images.jianshu.io/upload_images/11223715-36435762dd8844a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/927/format/webp)
正常的连接haproxy，传递增删盖查，其实是通过轮询的方式。选择mysql的节点。均匀的分发给mysql的实例。不会吧数据库的请求都集中在一个节点上。把请求分散出去，每个数据库实例获取到的请求就小很多了。这就是数据库的负载。

* 虚拟机重启后，发现pxc起不起来了
查看日志发现，node1无法启动，输入命令查看docker logs node1
 
It may not be safe to bootstrap the cluster from this node. It was not the last one to leave the cluster and may not contain all the updates. To force cluster bootstrap with this node, edit the grastate.dat file manually and set safe_to_bootstrap to 1 .
 
解决方案
 
/#查看node1挂载点的文件地址 docker volume inspect v1 /# 进入目录下 cd /var/lib/docker/volumes/v1/_data /#编辑文件grastate.dat vi grastate.dat

![](//upload-images.jianshu.io/upload_images/11223715-167e1c2dbdef8632.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-94dfc9eaedd61170.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

高可用负载均衡方案
目前haproxy只有一个，单haproxy不具备高可用，必须冗余设计。haproxy不能形成瓶颈。

![](//upload-images.jianshu.io/upload_images/11223715-7ed3955ae10eb0a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 虚拟IP技术
haproxy双机互备离不开一个关键的技术，这个技术是虚拟IP，linux可以在一个网卡内定义多个虚拟IP，得把这些IP地址定义到一个虚拟IP。

* 利用keepalived实现双机热备
定义出来一个虚拟IP，这个方案叫双机热备，准备2个keepalived，keepalived 就是为了抢占虚拟IP的，谁手快谁能抢到，没抢到的处于等待的状态。抢到的叫做主服务器，未抢到的叫做备服务器。两个keepalived之前有心跳检测的，当备用的检测到主服务挂了，就立马抢占虚拟IP。

![](//upload-images.jianshu.io/upload_images/11223715-0e21801065f9c519.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/851/format/webp)

* Haproxy双机热备方案
 

![](//upload-images.jianshu.io/upload_images/11223715-4ff84aec60aec08f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
* 安装keepalived
keepalived必须在haproxy所在的容器之内，也可以在docker仓库里面下载一个haproxy-keepalived的镜像。这里直接在容器内安装keepalived。
 
docker exec -it h1 /bin/bash /#写入dns，防止apt-get update找不到服务器 echo "nameserver 8.8.8.8" | tee /etc/resolv.conf > /dev/null apt-get clean apt-get update apt-get install vim vi /etc/apt/sources.list

![](//upload-images.jianshu.io/upload_images/11223715-e9045faad883d88a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

sources.list 添加下面的内容
 
deb http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb-src http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse deb http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted deb http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted deb-src http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted

![](//upload-images.jianshu.io/upload_images/11223715-681b00d097083d25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 更新apt源
apt-get clean apt-get update apt-get install keepalived apt-get install vim

![](//upload-images.jianshu.io/upload_images/11223715-2d9bbae4787e98a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* keepalived配置文件
容器内的路径：/etc/keepalived/keepalived.conf
 
vi /etc/keepalived/keepalived.conf

![](//upload-images.jianshu.io/upload_images/11223715-2c84fa17d3a57b24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/647/format/webp)

1. VI_1 名称可以自定义
1. state MASTER | keepalived的身份（MASTER主服务器，BACKUP备份服务器，不会抢占虚拟机ip）。如果都是主MASTER的话，就会进行互相争抢IP，如果抢到了就是MASTER，另一个就是SLAVE。
1. interface网卡，定义一个虚拟IP定义到那个网卡上边。网卡设备的名称。eth0是docker的虚拟网卡，宿主机是可以访问的。
1. virtual_router_id 51 | 虚拟路由标识，MASTER和BACKUP的虚拟路由标识必须一致。标识可以是0-255。
1. priority 100 | 权重。MASTER权重要高于BACKUP 数字越大优选级越高。可以根据硬件的配置来完成，权重最大的获取抢到的级别越高。
1. advert_int 1 | 心跳检测。MASTER与BACKUP节点间同步检查的时间间隔，单位为秒。主备之间必须一致。
1. authentication | 主从服务器验证方式。主备必须使用相同的密码才能正常通信。进行心跳检测需要登录到某个主机上边所有有账号密码。
1. virtual_ipaddress | 虚拟ip地址，可以设置多个虚拟ip地址，每行一个。根据上边配置的eth0上配置的ip。

* 启动keeplived
容器内启动
 
service keepalived start

![](//upload-images.jianshu.io/upload_images/11223715-55029ceee3d1bf5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/812/format/webp)

宿主机ping这个ip
 
ping 172.18.0.201

![](//upload-images.jianshu.io/upload_images/11223715-afaaaa2f617be353.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

创建haproxy2容器,并配置与haproxy1相同的环境
因为要保证有2个haproxy 和keepalived，这次就不那么麻烦了。直接对一个镜像里面包括keeplived 和 haproxy。

* 宿主机创建文件
mkdir haproxy/h2 cd haproxy/h2 vi haproxy.cfg vi keepalived.cfg

![](//upload-images.jianshu.io/upload_images/11223715-6de6c03c8c13cc56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/735/format/webp)

* haproxy.cfg
global /#工作目录 chroot /usr/local/etc/haproxy /#日志文件，使用rsyslog服务中local5日志设备（/var/log/local5），等级info log 127.0.0.1 local5 info /#守护进程运行 daemon defaults log global mode http /#日志格式 option httplog /#日志中不记录负载均衡的心跳检测记录 option dontlognull /#连接超时（毫秒） timeout connect 5000 /#客户端超时（毫秒） timeout client 50000 /#服务器超时（毫秒） timeout server 50000 /#监控界面 listen admin_stats /#监控界面的访问的IP和端口 bind 0.0.0.0:8888 /#访问协议 mode http /#URI相对地址 stats uri /dbs /#统计报告格式 stats realm Global\ statistics /#登陆帐户信息 stats auth admin:abc123456 /#数据库负载均衡 listen proxy-mysql /#访问的IP和端口 bind 0.0.0.0:3306 /#网络协议 mode tcp /#负载均衡算法（轮询算法） /#轮询算法：roundrobin /#权重算法：static-rr /#最少连接算法：leastconn /#请求源IP算法：source balance roundrobin /#日志格式 option tcplog /#在MySQL中创建一个没有权限的haproxy用户，密码为空。Haproxy使用这个账户对MySQL数据库心跳检测 option mysql-check user haproxy server MySQL_1 172.18.0.2:3306 check weight 1 maxconn 2000 server MySQL_2 172.18.0.3:3306 check weight 1 maxconn 2000 server MySQL_3 172.18.0.4:3306 check weight 1 maxconn 2000 server MySQL_4 172.18.0.5:3306 check weight 1 maxconn 2000 server MySQL_5 172.18.0.6:3306 check weight 1 maxconn 2000 /#使用keepalive检测死链 option tcpka

* keepalived.cfg
vrrp_instance VI_1 { state MASTER interface eth0 virtual_router_id 51 priority 100 advert_int 1 authentication { auth_type PASS auth_pass 123456 } virtual_ipaddress { 172.18.0.201 } }

* docker的方式一下安装好 haproxy 和keepalived
[https://hub.docker.com/r/pelin/haproxy-keepalived/](https://links.jianshu.com/go?to=https%3A%2F%2Fhub.docker.com%2Fr%2Fpelin%2Fhaproxy-keepalived%2F)
映射端口更改为4003 4004 name修改h2
 
docker run -it -d --privileged -p 4003:8888\ -p 4004:3306 \ -v /root/haproxy/h2/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg \ -v /root/haproxy/h2/keepalived.conf:/etc/keepalived/keepalived.conf \ --name haproxy-keepalived \ --net=net1 \ --name h2 \ --ip 172.18.0.8 \ pelin/haproxy-keepalived

![](//upload-images.jianshu.io/upload_images/11223715-e85e87c0a56f0483.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-2ac185b618e0cea6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-5481d6a06d2937cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/927/format/webp)

宿主机安装keepalived
yum -y install keepalived vi /etc/keepalived/keepalived.conf /#删除这个表里之前存在的内容

![](//upload-images.jianshu.io/upload_images/11223715-817a9fc7faec3090.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* keepalived.cof
vrrp_instance VI_1 { state MASTER interface eth0 virtual_router_id 51 priority 100 advert_int 1 authentication { auth_type PASS auth_pass 1111 } virtual_ipaddress { 192.168.66.200 } } virtual_server 192.168.66.200 8888 { delay_loop 3 lb_algo rr lb_kind NAT persistence_timeout 50 protocol TCP real_server 172.18.0.201 8888 { weight 1 } } virtual_server 192.168.66.200 3306 { delay_loop 3 lb_algo rr lb_kind NAT persistence_timeout 50 protocol TCP real_server 172.18.0.201 3306 { weight 1 } }

![](//upload-images.jianshu.io/upload_images/11223715-a10a6a1306f187d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 启动宿主机
service keepalived start

![](//upload-images.jianshu.io/upload_images/11223715-84847d8beee2b581.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-f1eea48cecd3031c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/924/format/webp)

虚拟机端口转发 外部无法访问
WARNING: IPv4 forwarding is disabled. Networking will not work.

* 解决方案
宿主机修改
 
vi /etc/sysctl.conf /# 添加net.ipv4.ip_forward=1 systemctl restart network

![](//upload-images.jianshu.io/upload_images/11223715-3875885526ad1535.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

PS：如果通过docker的方式直接拉取haproxy和keepalived镜像，比直接在镜像里面安装应用方便很多，建议各位老铁尽量避免在容器内安装应用，这样真心麻烦不爽，别人封装的镜像根据pull的量好好看看api就可以使用了。像h1如果容器stop后，重新start，还需要进入容器把keeplived给起起来。而h2直接start里面的haproxy和keeplived，同时都起起来了。 两个容器的采用的热备的方案，让用户毫无感知，切换ip的形式真是美滋滋。mysql集群的高性能，高负载，高可用基本完成了，可用按照这个思路搭建不同的主机下。

作者：IT人故事会
链接：https://www.jianshu.com/p/c623713a532c
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

