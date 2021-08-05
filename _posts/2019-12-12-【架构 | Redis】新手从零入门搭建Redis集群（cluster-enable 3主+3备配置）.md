# 【架构 | Redis】新手从零入门搭建Redis集群（cluster-enable 3主+3备配置）

在开始redis集群搭建之前，我们先简单回顾一下redis单机版的搭建过程

下载redis压缩包，然后解压压缩文件；
进入到解压缩后的redis文件目录（此时可以看到Makefile文件），编译redis源文件；
把编译好的redis源文件安装到/usr/local/redis目录下，如果/local目录下没有redis目录，会自动新建redis目录；
进入/usr/local/redis/bin目录，直接./redis-server启动redis（此时为前端启动redis）；
将redis启动方式改为后端启动，具体做法：把解压缩的redis文件下的redis.conf文件复制到/usr/local/redis/bin目录下，然后修改该redis.conf文件->daemonize：no 改为daemonize：yse；
在/bin目录下通过./redis-server redis.conf启动redis（此时为后台启动）。
综上redis单机版安装启动完成。

**单机版：[https://blog.csdn.net/qq_28505809/article/details/84953965](https://blog.csdn.net/qq_28505809/article/details/84953965)**

----

## 一、Redis Cluster（Redis集群）简介

* redis是一个开源的key value存储系统，受到了广大互联网公司的青睐。redis3.0版本之前只支持单例模式，在3.0版本及以后才支持集群，我这里用的是redis3.0.0版本；
* redis集群采用P2P模式，是完全去中心化的，不存在中心节点或者代理节点；
* redis集群是没有统一的入口的，客户端（client）连接集群的时候连接集群中的任意节点（node）即可，集群内部的节点是相互通信的（PING-PONG机制），每个节点都是一个redis实例；
* 为了实现集群的高可用，即判断节点是否健康（能否正常使用），redis-cluster有这么一个投票容错机制：如果集群中超过半数的节点投票认为某个节点挂了，那么这个节点就挂了（fail）。这是判断节点是否挂了的方法；
* 那么如何判断集群是否挂了呢? -> 如果集群中任意一个节点挂了，而且该节点没有从节点（备份节点），那么这个集群就挂了。这是判断集群是否挂了的方法；
* 那么为什么任意一个节点挂了（没有从节点）这个集群就挂了呢？ -> 因为集群内置了16384个slot（哈希槽），并且把所有的物理节点映射到了这16384[0-16383]个slot上，或者说把这些slot均等的分配给了各个节点。当需要在Redis集群存放一个数据（key-value）时，redis会先对这个key进行crc16算法，然后得到一个结果。再把这个结果对16384进行求余，这个余数会对应[0-16383]其中一个槽，进而决定key-value存储到哪个节点中。所以一旦某个节点挂了，该节点对应的slot就无法使用，那么就会导致集群无法正常工作。
* 综上所述，每个Redis集群理论上最多可以有16384个节点。

## 二、集群搭建需要的环境

1. Redis集群至少需要3个节点，因为投票容错机制要求超过半数节点认为某个节点挂了该节点才是挂了，所以2个节点无法构成集群。
1. 要保证集群的高可用，需要每个节点都有从节点，也就是备份节点，所以Redis集群至少需要6台服务器。因为我没有那么多服务器，也启动不了那么多虚拟机，所在这里搭建的是伪分布式集群，即一台服务器虚拟运行6个redis实例，修改端口号为（7001-7006），当然实际生产环境的Redis集群搭建和这里是一样的。
1. 安装ruby

## 三、集群搭建具体步骤如下（注意要关闭防火墙）

* 3.1 在usr/local目录下新建redis-cluster目录，用于存放集群节点

![新建Redis集群目录](https://img-blog.csdn.net/20181001140534311?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

* 3.2 把redis目录下的bin目录下的所有文件复制到/usr/local/redis-cluster/redis01目录下，不用担心这里没有redis01目录，会自动创建的。操作命令如下（注意当前所在路径）：
cp -r redis/bin/ redis-cluster/redis01

![在这里插入图片描述](https://img-blog.csdn.net/20181001141620400?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

* 3.3 删除redis01目录下的快照文件dump.rdb，并且修改该目录下的redis.cnf文件，具体修改两处地方：一是端口号修改为7001，二是开启集群创建模式，打开注释即可。分别如下图所示：

删除dump.rdb文件

![删除dump.rdb文件](https://img-blog.csdn.net/2018100114273634?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

修改端口号为7001,默认是6379

![在这里插入图片描述](https://img-blog.csdn.net/20181001142902121?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

将cluster-enabled yes 的注释打开

![在这里插入图片描述](https://img-blog.csdn.net/20181001142941621?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

* 3.4 将redis-cluster/redis01文件复制5份到redis-cluster目录下（redis02-redis06），创建6个redis实例，模拟Redis集群的6个节点。然后将其余5个文件下的redis.conf里面的端口号分别修改为7002-7006。分别如下图所示：

创建redis02-06目录

![在这里插入图片描述](https://img-blog.csdn.net/20181001150344737?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

分别修改redis.conf文件端口号为7002-7006

![在这里插入图片描述](https://img-blog.csdn.net/20181001150609235?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

* 3.5 接着启动所有redis节点，由于一个一个启动太麻烦了，所以在这里创建一个批量启动redis节点的脚本文件，[命令为start-all.sh](http://xn--start-all-fp6nn2dk05a.sh/)，文件内容如下：
cd redis01 ./redis-server redis.conf cd .. cd redis02 ./redis-server redis.conf cd .. cd redis03 ./redis-server redis.conf cd .. cd redis04 ./redis-server redis.conf cd .. cd redis05 ./redis-server redis.conf cd .. cd redis06 ./redis-server redis.conf cd ..

* 3.6 创建好启动脚本文件之后，需要修改该脚本的权限，使之能够执行，指令如下：
 

chmod +x start-all.sh
 ![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

![在这里插入图片描述](https://img-blog.csdn.net/20181001151921344?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

* 3.7 执行start-all.sh脚本，启动6个redis节点

![在这里插入图片描述](https://img-blog.csdn.net/20181001152259456?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

* 3.8 ok，至此6个redis节点启动成功，接下来正式开启搭建集群，以上都是准备条件。大家不要觉得图片多看起来冗长所以觉得麻烦，其实以上步骤也就一句话的事情：创建6个redis实例（6个节点）并启动。

要搭建集群的话，需要使用一个工具（脚本文件），这个工具在redis解压文件的源代码里。因为这个工具是一个ruby脚本文件，所以这个工具的运行需要ruby的运行环境，就相当于java语言的运行需要在jvm上。所以需要安装ruby，指令如下：
yum install ruby
 ![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

然后需要把ruby相关的包安装到服务器，我这里用的是redis-3.0.0.gem，大家需要注意的是：redis的版本和ruby包的版本最好保持一致。

将Ruby包安装到服务器：需要先下载再安装，如图

![在这里插入图片描述](https://img-blog.csdn.net/20181001155254288?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​
安装命令如下：
gem install redis-3.0.0.gem
 ![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

![在这里插入图片描述](https://img-blog.csdn.net/20181001155619828?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

* 3.9 上一步中已经把ruby工具所需要的运行环境和ruby包安装好了，接下来需要把这个ruby脚本工具复制到usr/local/redis-cluster目录下。那么这个ruby脚本工具在哪里呢？之前提到过，在redis解压文件的源代码里，即redis/src目录下的redis-trib.rb文件。

![在这里插入图片描述](https://img-blog.csdn.net/20181001160619425?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

![在这里插入图片描述](https://img-blog.csdn.net/20181001160630117?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

* 3.10 将该ruby工具（redis-trib.rb）复制到redis-cluster目录下，指令如下：
cp redis-trib.rb /usr/local/redis-cluster
 ![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

然后使用该脚本文件搭建集群，指令如下：

./redis-trib.rb create --replicas 1 47.106.219.251:7001 47.106.219.251:7002 47.106.219.251:7003 47.106.219.251:7004 47.106.219.251:7005 47.106.219.251:7006
 ![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

注意：此处大家应该根据自己的服务器ip输入对应的ip地址！

![在这里插入图片描述](https://img-blog.csdn.net/20181001161520287?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

中途有个地方需要手动输入yes即可

![在这里插入图片描述](https://img-blog.csdn.net/20181001161757158?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

至此，Redi集群搭建成功！大家注意最后一段文字，显示了每个节点所分配的slots（哈希槽），这里总共6个节点，其中3个是从节点，所以3个主节点分别映射了0-5460、5461-10922、10933-16383solts。

* 3.11 最后连接集群节点，连接任意一个即可：
redis01/redis-cli -p 7001 -c
 ![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

注意：一定要加上-c，不然节点之间是无法自动跳转的！如下图可以看到，存储的数据（key-value）是均匀分配到不同的节点的：

![在这里插入图片描述](https://img-blog.csdn.net/20181001162628508?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODE1NzU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

## 四、结语

终于搭建好了Redis集群。

最后，加上两条redis集群基本命令：

**1.查看当前集群信息**
cluster info
 ![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

**2.查看集群里有多少个节点**

cluster nodes
 ![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

 


