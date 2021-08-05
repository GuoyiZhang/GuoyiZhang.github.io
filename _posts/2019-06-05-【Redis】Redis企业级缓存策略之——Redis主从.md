# 【Redis】Redis企业级缓存策略之——Redis主从


**一：企业常见的Redis主从架构**

①一主多从

②一主多从从

![](https://img-blog.csdnimg.cn/20190605200639636.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI4NTA1ODA5,size_16,color_FFFFFF,t_70)

 

**二：主从复制的优点**

（1）高可用性

在一个Redis集群中，如果master宕机，slave可以介入并取代master的位置，因此对于整个Redis服务来说不至于提供不了 服务，这样使得整个Redis服务足够安全。

（2）高性能

在一个Redis集群中，master负责写请求，slave负责读请求，这么做一方面通过将读请求分散到其他机器从而大大减少了 master服务器的压力，另一方面slave专注于提供读服务从而提高了响应和读取速度。 

（3）水平拓展性

通过增加slave机器可以横向（水平）扩展Redis服务的整个查询服务的能力。 

 

**三：主从复制能够解决什么问题，同时又带来了什么问题**

复制提供了高可用性的解决方案，但同时引入了分布式计算的复杂度问题，认为有两个核心问题： 

（1）数据一致性问题：如何保证master服务器写入的数据能够及时同步到slave机器上。 

（2）读写分离：如何在客户端提供读写分离的实现方案，通过客户端实现将读写请求分别路由到master和slave实例上。 上面两个问题，尤其是第一个问题是Redis服务实现一直在演变，致力于解决的一个问题:复制实时性和数据一致性矛盾 

**总结：**Redis提供了提高数据一致性的解决方案，一致性程度的增加虽然使得我能够更信任数据，但是更好的一致性方案通常伴随着性能的损失，从而减少了吞吐量和服务能力。然而我们希望系统的性能达到最优，则必须要牺牲一致性的程度，因此Redis的复制实时性和数据一致性是存在矛盾的。

 

**四：主从复制过程详解**

![](https://img-blog.csdnimg.cn/2019060520070773.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI4NTA1ODA5,size_16,color_FFFFFF,t_70)
1、slave向master发送sync命令。  2、master开启子进程来将dataset写入rdb文件，同时将子进程完成之前接收到的写命令缓存起来。  3、子进程写完，父进程得知，开始将RDB文件发送给slave。 master发送完RDB文件，将缓存的命令也发给slave。 master增量的把写命令发给slave。 注意：slave与master断开后可自动重新连接master。在redis2.8版本之前，每当slave进程挂掉重新连接master的时候都会开始新的一轮全量复制。如果 master同时接收到多个slave的同步请求，则master只需要备份一次RDB文件。

相关知识点：Redis持久化存储策略之**RDB**与**AOF**

①RDB：snapshotting 数据快照, 二进制格式；     1）策略描述：按事先定制的策略，周期性地将数据从内存同步至磁盘；数据文件默认为dump.rdb；     2）快照方法：redis-cli 显式使用sava或bgsave命令来手动启动快照保存机制         SAVE：同步，即在主线程中保存快照，此时会阻塞所有客户端请求；          BGSAVE：异步；backgroud ②AOF：Append Only File, fsync     1）策略描述：记录每次写操作至指定的文件尾部实现的持久化；当redis重启时，可通过重新执行文件中的命令在内存中重建出数据库；     2）redis-cli命令         BGREWRITEAOF：AOF文件重写         不会读取正在使用的AOF文件，而是通过将内存中的数据以命令的方式保存至临时文件中，完成之后替换原来的AOF文件； 注意：①Redis默认使用RDB文件进行持久化存储，可通过修改redis主配置文件，开启AOF功能    ②两文件默认存储在/var/lib/redis/目录下

 

**五：Redis主从配置过程**

1、配置环境

master：172.17.214.73

slave：172.17.214.74

slave：172.17.214.75

2、配置前准备

①各服务器安装redis

②备份主配置文件

③确保各节点防火墙关闭、selinux关闭，时钟同步（否则容易被认为已超时）

3、配置过程

①修改所有主机主配置文件
vim /etc/redis.conf     daemonize yes     /#以守护进程启动     bind    127.0.0.1   /#监听本机IP 注意：工作环境中应将bind绑定在127.0.0.1上，且修改默认端口，防止Redis相关的”key“网络植入攻击

 

②修改从服务器主配置文件
/#/#/# REPLICATION /#/#/#  slaveof 192.168.1.29 6379     /#指定主节点地址IP port /#masterauth             /#如果设置了访问认证就需要设定此项。  slave-server-stale-data yes     /#当slave与master连接断开或者slave他 r正处于同步状态时，如果slave收到请求允许响应，no表示返回错误。  slave-read-only yes         /#slave节点为只读 slave-priority 100         /#设定此节点的优先级，是否优先被同步（值越小，优先级越高）

 

③测试结果 
主节点上新建一个key value，查看从节点是否同步
