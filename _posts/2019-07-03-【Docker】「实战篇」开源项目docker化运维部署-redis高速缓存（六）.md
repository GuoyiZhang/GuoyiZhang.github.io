---
layout:     post
title:      【Docker】「实战篇」开源项目docker化运维部署-redis高速缓存（六）
subtitle:   【Docker】「实战篇」开源项目docker化运维部署-redis高速缓存（六）
date:       2019-07-03 17:38:37
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 开发工具
    - 「实战篇」开源项目docker化运维部署
---

>【Docker】「实战篇」开源项目docker化运维部署-redis高速缓存（六）

# 【Docker】「实战篇」开源项目docker化运维部署-redis高速缓存（六）

现在一般的项目都会用到redis做缓存，也不免有老铁没用过，我就一起说下吧。源码：[https://github.com/limingios/netFuture/tree/master/redis-cluster](https://github.com/limingios/netFuture/tree/master/redis-cluster)

![](//upload-images.jianshu.io/upload_images/11223715-a05179db2133ba95.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/405/format/webp)

redis

* 官网
[https://redis.io/](https://redis.io/)
Redis是一个开源（BSD许可）的内存数据结构存储，用作数据库、缓存和消息代理。它支持诸如字符串、散列、列表、集合、带有范围查询的排序集、位图、日志、带有半径查询的地理空间索引和流之类的数据结构。Redis具有内置的复制、Lua脚本、LRU驱逐、事务和不同级别的磁盘持久性，并通过Redis Sentinel和Redis Cluster自动分区提供高可用性。

![](//upload-images.jianshu.io/upload_images/11223715-2b1e4a25ff35bec5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 历史
2008年，意大利一家创业公司Merzia的创始人Salvatore Sanfilippo为了避免MySQL的低性能，亲自定做一个数据库，并于2009年开发完成，这个就是Redis。
短短几年，用户数据量猛增。国内如新浪微博、街旁和知乎等，国外如GitHub、暴雪等，都是Redis的用户。世界上最大规模的Redis缓存，就是新浪微博团队打造的。热点新闻的时候。Redis可以达到最多每秒10万的读写。

* 高速缓存介绍

1. 高速缓存利用内存保持数据，读写速度远超过硬盘
1. 高速缓存可以减少IO操作，降低IO压力
微信红包就是很好的例子，在发红包的时候，红包信息就保存在缓存中，抢的人也是从高速缓存中取。春节当天几个亿的人来抢也保持系统的稳定。

1. 一般的应用，都分为常用和个性化，个性化可能是从数据库中获取的。但是常用的可能就是从高速缓存中获取的。

* Redis集群介绍
Redis目前的集群方案为以下几种：

1. RedisCluster：官方推荐，没有中心点（主节点不是中心节点，而是保存数据最多的，最新的，同步后主节点就消失了）。
1. Codis：中间件，存在中心节点（中心节点挂了，彻底玩完）。
1. Twemproxy：中间件产品，存在中心节点。

* RedisCluster

1. 无中心节点，客户端与redis节点直连，不需要中间代理层（很类似PXC）
1. 数据可以被分片存储（每个节点存储的内容是不一样的）
1. 管理方便，后续可自行增加或者摘除节点

* 本次搭建的Redis节点的示意图

![](//upload-images.jianshu.io/upload_images/11223715-cac698dc0da9345f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 主从同步
上边说过，RedisCluster的数据是分片存储的，如果redis挂了就会丢失一部分的数据。为了避免这个问题的产生，就必须引入主从同步的机制

1. Redis集群中的数据复制是通过主从同步来实现的。
1. 主节点（Master）把数据分发给从节点（Slave）
1. 主从同步的好处在于高可用，Rredis节点有冗余设计

* Redis集群高可用

1. Redis集群中应该包含奇数个Master，至少应该是3个，如果其中一个挂的，剩余奇数个可以进行选举至少过半的情况。很容易选择到master节点。
1. Redis集群中每个Master都应该有Slave

* 为什么Redis不搭建负载均衡
因为本身前后端分离项目，请求后端的时候，后端对请求已经做了负载均衡所以Redis不需要做负载均衡。

搭建集群

应用IP地址服务配置安装应用安装方式docker-mysql192.168.66.101docker-redis-cluster双核 8g内存docker-redis-clusterdocker

(1). 虚拟机vagrant讲述安装的步骤

vagrant up

(2).机器window/mac开通远程登录root用户下

su - /# 密码 vagrant /#设置 PasswordAuthentication yes vi /etc/ssh/sshd_config sudo systemctl restart sshd

![](//upload-images.jianshu.io/upload_images/11223715-9ddf93087ce3e321.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/751/format/webp)

* 创建文件夹，配置
mkdir redis-cluster cd redis-cluster mkdir r1 cd r1 vi redis.conf mkdir data cd ~

![](//upload-images.jianshu.io/upload_images/11223715-0a30402187a2e770.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/753/format/webp)

![](//upload-images.jianshu.io/upload_images/11223715-0971f61add33e7f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/709/format/webp)

* redis.conf
配置了5个地方

1. daemonize yes
以后台进程运行

1. cluster-enabled yes
开启集群

1. cluster-config-file 150000
超时时间

1. appendonly yes
开启AOF模式，保存文件的形式

1. requirepass idig8.com
认证密码

1. cluster-config-file nodes.conf
集群配置文件
 
直接看github我提交的源码吧

一共要创建6个redis集群

* 创建容器（r1）
想加上安全验证，但是不生效，查了下daemonize yes，他的作用是是否开启守护进程模式，在该模式下，redis会在后台运行，并将进程pid号写入至redis.conf选项pidfile设置的文件中，此时redis将一直运行，除非手动kill该进程。所以进入这个容器内手动选择加载配置文件。
 
docker run -it -d \ -v /root/redis-cluster/r1/redis.conf:/etc/redis/redis.conf \ --name r1 -p 5001:6379 \ --net=net2 \ --ip 172.19.0.2 \ zhugeaming1314/redis bash

* 配置启动
docker exec -it r1 bash cd /usr/redis/src ./redis-server /etc/redis/redis.conf

![](//upload-images.jianshu.io/upload_images/11223715-33712a9c9e023f66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/464/format/webp)

* 创建容器（r2）
docker run -it -d \ -v /root/redis-cluster/r2/redis.conf:/etc/redis/redis.conf \ --name r2 -p 5002:6379 \ --net=net2 \ --ip 172.19.0.3 \ zhugeaming1314/redis bash

* 配置启动
docker exec -it r2 bash cd /usr/redis/src ./redis-server /etc/redis/redis.conf

![](//upload-images.jianshu.io/upload_images/11223715-3cb8a658eae72553.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/445/format/webp)

* 创建容器（r3）
docker run -it -d \ -v /root/redis-cluster/r3/redis.conf:/etc/redis/redis.conf \ --name r3 -p 5003:6379 \ --net=net2 \ --ip 172.19.0.4 \ zhugeaming1314/redis bash

* 配置启动
docker exec -it r3 bash cd /usr/redis/src ./redis-server /etc/redis/redis.conf

![](//upload-images.jianshu.io/upload_images/11223715-2b04f577d7f21414.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/480/format/webp)

* 创建容器（r4）
docker run -it -d \ -v /root/redis-cluster/r4/redis.conf:/etc/redis/redis.conf \ --name r4 -p 5004:6379 \ --net=net2 \ --ip 172.19.0.5 \ zhugeaming1314/redis bash

* 配置启动
docker exec -it r4 bash cd /usr/redis/src ./redis-server /etc/redis/redis.conf

![](//upload-images.jianshu.io/upload_images/11223715-c4ab24fcdf462598.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/454/format/webp)

* 创建容器（r5）
docker run -it -d \ -v /root/redis-cluster/r5/redis.conf:/etc/redis/redis.conf \ --name r5 -p 5005:6379 \ --net=net2 \ --ip 172.19.0.6 \ zhugeaming1314/redis bash

* 配置启动
docker exec -it r5 bash cd /usr/redis/src ./redis-server /etc/redis/redis.conf

![](//upload-images.jianshu.io/upload_images/11223715-8dd867599b8e5218.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/511/format/webp)

* 创建容器（r6）
docker run -it -d \ -v /root/redis-cluster/r6/redis.conf:/etc/redis/redis.conf \ --name r6 -p 5006:6379 \ --net=net2 \ --ip 172.19.0.7 \ zhugeaming1314/redis bash

* 配置启动
docker exec -it r6 bash cd /usr/redis/src ./redis-server /etc/redis/redis.conf

![](//upload-images.jianshu.io/upload_images/11223715-5e017c798b841e7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/460/format/webp)

redis-trib.rb
redis内自带集群工具redis-trib.rb，操作redis-trib需要很多指令很麻烦。建议使用我提供的镜像，里面什么都装好了老铁就根据我的命令操作就可以了 。

![](//upload-images.jianshu.io/upload_images/11223715-5723cb60cab06725.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

* 创建集群命令
docker exec -it r1 bash cd /usr/redis mkdir cluster cd src cp redis-trib.rb ../cluster cd ../cluster ./redis-trib.rb create --replicas 1 172.19.0.2:6379 172.19.0.3:6379 172.19.0.4:6379 172.19.0.5:6379 172.19.0.6:6379 172.19.0.7:6379

![](//upload-images.jianshu.io/upload_images/11223715-5ba6bbd3c356be2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

image.png

* 查看集群信息
docker exec -it r1 bash /usr/redis/src/redis-cli -c cluster nodes

!/upload-images.jianshu.io/upload_images/11223715-0123418e8224c25c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 安装集群的时候报的错误
can't connect to node 172.19./* redis.conf文件

1. bind 127.0.0.1 注释掉
1. requirepass idig8.com 注释掉

redis集群密码设置

* 密码设置(推荐)
方式一：修改所有Redis集群中的redis.conf文件加入：
 
masterauth idig8.com requirepass idig8.com

说明：这种方式需要重新启动各节点

方式二：进入各个实例进行设置：
 
./redis-cli -c -p 6379 config set masterauth idig8.com config set requirepass idig8.com config rewrite

之后分别使用./redis-cli -c -p 6379，./redis-cli -c -p 6379…..命令给各节点设置上密码。

注意：各个节点密码都必须一致，否则Redirected就会失败， 推荐这种方式，这种方式会把密码写入到redis.conf里面去，且不用重启。

用方式二修改密码，./redis-trib.rb check 172.19.0.2:6379执行时可能会报[ERR] Sorry, can't connect to node 172.19.0.2:6379，因为6379的redis.conf没找到密码配置。

* 设置密码之后如果需要使用redis-trib.rb的各种命令
如：./redis-trib.rb check 127.0.0.1:6379，则会报错ERR] Sorry, can’t connect to node 127.0.0.1:6379
解决办法：vim /usr/local/rvm/gems/ruby-2.3.3/gems/redis-4.0.0/lib/redis/client.rb，然后修改passord
 
class Client DEFAULTS = { :url => lambda { ENV["REDIS_URL"] }, :scheme => "redis", :host => "127.0.0.1", :port => 6379, :path => nil, :timeout => 5.0, :password => "idig8.com", :db => 0, :driver => nil, :id => nil, :tcp_keepalive => 0, :reconnect_attempts => 1, :inherit_socket => false }

**注意：client.rb路径可以通过find命令查找：find / -name 'client.rb'**

带密码访问集群
./redis-cli -c -p 6379-a idig8.com

PS：整个redis集群已经安装完毕，3个master3个salve，如果1个master挂了对应的slave自动升级为master，挂的原来的master如果重新启动就变成了slave。我尝试用官方的docker镜像redis来进行全流程的安装，在docker run命令中加入配置文件启动，这种方式是有问题的，到创建集群的时候还是会报错的，还是建议用我的镜像，这样稳定些。并且里面自带redis-trib.rb。

作者：IT人故事会
链接：https://www.jianshu.com/p/31b16b34d675
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

