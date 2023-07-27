---
layout: post
title: ZooKeeper介绍以及应用
date: 2018-11-19 15:27:00.000000000 +09:00
tags: ZooKeeper 分布式配置 分布式锁 zab
---


# ZooKeeper介绍

**Note. 本文主要介绍[ZooKeeper的基本概念](https://zookeeper.apache.org/doc/current/zookeeperOver.html)，以及基于ZooKeeper的分布式配置平台demo和分布式锁，记录学习过程中的体会与总结。**

- [1. 一个分布式配置平台原型](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-11-19-ZooKeeper%E4%BB%8B%E7%BB%8D%E4%BB%A5%E5%8F%8A%E5%BA%94%E7%94%A8.md#1-%E4%B8%80%E4%B8%AA%E5%88%86%E5%B8%83%E5%BC%8F%E9%85%8D%E7%BD%AE%E5%B9%B3%E5%8F%B0%E5%8E%9F%E5%9E%8B)

- [2. ZooKeeper背景介绍](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-11-19-ZooKeeper%E4%BB%8B%E7%BB%8D%E4%BB%A5%E5%8F%8A%E5%BA%94%E7%94%A8.md#2-zookeeper%E8%83%8C%E6%99%AF%E4%BB%8B%E7%BB%8D)

- [3. ZooKeeper基础概念](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-11-19-ZooKeeper%E4%BB%8B%E7%BB%8D%E4%BB%A5%E5%8F%8A%E5%BA%94%E7%94%A8.md#3-zookeeper%E5%9F%BA%E7%A1%80%E6%A6%82%E5%BF%B5)
    
- [4. ZAB协议](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-11-19-ZooKeeper%E4%BB%8B%E7%BB%8D%E4%BB%A5%E5%8F%8A%E5%BA%94%E7%94%A8.md#4-zab%E5%8D%8F%E8%AE%AE)

- [5. 基于ZooKeeper的分布式锁](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-11-19-ZooKeeper%E4%BB%8B%E7%BB%8D%E4%BB%A5%E5%8F%8A%E5%BA%94%E7%94%A8.md#5-%E5%9F%BA%E4%BA%8Ezookeeper%E7%9A%84%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81)


## 1. 一个分布式配置平台原型

在介绍ZooKeeper之前，先介绍一个基于ZooKeeper的分布式配置平台demo。关于什么是分布式配置请参考文章：[一篇好TM长的关于配置中心的文章](http://jm.taobao.org/2016/09/28/an-article-about-config-center/)，这篇文章讲得很详细。简而言之，就是能够为分布式应用提供动态调整配置的平台，调整过程中不用停服、不用重新编译代码、打包、上线等等。我们基于ZooKeeper就很容易搭建出一个分布式配置平台原型。

下面是一个简单demo的网络拓扑图

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/%E5%88%86%E5%B8%83%E5%BC%8F%E9%85%8D%E7%BD%AEdemo%E6%8B%93%E6%89%91.png?raw=true" height="400" width="550">	
</div>

<p align="center">
  <b>图 1 demo网络拓扑图</b><br>
</p>

机器10.96.90.6是一台ZooKeeper Server，10.96.82.133和10.179.195.229是两台ZooKeeper Client，它们会不间断地打印内存中的变量`content`。我们平时也会遇到这样的场景，在程序里有个变量，是从配置文件里面加载的，然后在某个时刻去使用这个变量。但是需要更新变量的值的时候，一般的做法是先修改所有机器的配置文件然后重新编译、打包、部署。整个过程会需要停服，走一系列的发布流程，非常繁琐并且损失掉部分服务时间。

利用ZooKeeper的Watcher特性和ZAB协议，我们只需要通过一个命令或者一个请求就能轻松地完成上述场景的配置更新。后面会介绍Watcher和ZAB协议，基于[go-zookeeper的客户端](https://github.com/samuel/go-zookeeper)的实现如下：

```
var content string

func main() {
	unstop := make(chan int)
	c, _, err := zk.Connect([]string{"10.96.90.6"}, time.Second) //*10)
	if err != nil {
		panic(err)
	}
	bytes, _, ch, err := c.GetW("/didi")
	if err != nil {
		panic(err)
	}
	content = string(bytes)
	go func() {
		for {
			<-ch
			bytes, _, ch, err = c.GetW("/didi")
			if err != nil {
				panic(err)
			}
			content = string(bytes)
		}
	}()
	go func() {
		for {
			fmt.Printf("%v\n", content)
			time.Sleep(2 * time.Second)
		}
	}()

	<-unstop
}
```

上面这段代码的逻辑是：先连接zk server，然后读取路径/didi下的数据并赋值给内存变量content，同时对路径/didi设置Watcher。然后开启了两个goroutine,其中一个是每隔2秒打印一次content的值；另外一个是阻塞等待Watcher，监测/didi路径是否有变动，如果有的话，则获取当前数据并更新content的值，然后继续对/didi路径进行监测。这里要注意的是每次设置一次Watcher只会触发一次通知，一旦监测的znode发生了变化，Watcher就会触发并被移除掉。所以如果想一直监测某个znode，需要不断对znode设置Watcher。

由于目前/didi路径的znode数据为hello，两台ZooKeeper Client同时运行后，都会输出hello，如下：

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/disf_demo_pre.png?raw=true" height="550" width="1000">	
</div>

<p align="center">
  <b>图 2 修改znode数据前效果图</b><br>
</p>

接下来修改znode的数据为world，可以看到图3，两台机器现在的输出几乎同时变为world。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/disf_demo_after.png?raw=true" height="550" width="1000">	
</div>

<p align="center">
  <b>图 3 修改znode数据后效果图</b><br>
</p>

通过ZooKeeper，上面寥寥数行的代码就能起搭建起一个分布式配置平台demo，接下来会对ZooKeeper的背景、基础概念和使用的一致性协议稍作介绍。

## 2. ZooKeeper背景介绍

ZooKeeper是一个面向分布式应用的开源协调服务，像Hadoop生态中的Pig、Hive等项目都有使用，所以看起来就像是一个动物管理员，故名之ZooKeeper。

常见使用场景有以下几种：
1. 配置管理
2. 分布式锁
3. 集群选举
4. 消息订阅

本文就选取了1、2两种场景作为介绍。

**ZooKeeper的设计原则：**
- 简单
- 分布式
- 有序
- 速度快

图4是ZK Server和Client的关系图，ZooKeeper允许分布式进程通过共享的层级命名空间相互协调，该命名空间与标准文件系统类似地组织。 名称空间由数据寄存器组成，这些数据寄存器也叫做`znodes`,与unix标准文件目录类似。 与专为存储而设计的典型文件系统不同，ZooKeeper数据保存在内存中，这意味着ZooKeeper可以实现高吞吐量和低延迟数量。但也决定了znode不适用于作为数仓，存储大量的数据。

ZooKeeper实现非常重视性能，高可用性，严格有序的访问。ZooKeeper的性能决定了它能够在大型分布式系统中使用，可靠性方面使其不会遭受单点故障问题。严格排序能够让客户端实现复杂的同步组件。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/zkservice.jpg?raw=true">	
</div>

<p align="center">
  <b>图 4 ZooKeeper Sever&Client拓扑图</b><br>
</p>

## 3. ZooKeeper基础概念

接下来对ZooKeeper的一些基础概念作进一步说明，

- **首先是数据模型和分层命名空间**，如图5所示：

ZooKeeper通过类似unix的文件系统来存储数据，ZooKeeper Client再基于这些数据作协调，就像在同一台机器上操作文件一样。每个znode可以看作是一个文件夹，下面可以存储文件或者文件夹。但是与一般的文件夹系统又不一样，自身可以存储数据。根目录是‘/’，每层路径以'/'分割，整体构成一棵树型结构。**删除znode的时候，只能删除没有子节点的znode.**

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/zknamespace.jpg?raw=true">	
</div>

<p align="center">
  <b>图 5 ZooKeeper数据模型图</b><br>
</p>

- **节点(znode)&临时节点(ephemeral node)**

znode存储容量很小，大概几B～几KB，并且会维护以下数据结构：

而临时节点是一种特殊的znode，只要创建znode的会话处于活动状态，就会存在这些znode。 会话结束时，znode将被删除。这种节点在实现分布式锁等场景下非常有用。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/zk_stat.png?raw=true" height="400" width="600">	
</div>

<p align="center">
  <b>图 6 znode结构图</b><br>
</p>

- **Watcher机制**

ZooKeeper支持Watcher机制。 客户端可以在znodes上设置Watcher， 当znode更改时，将触发并删除Watcher。 当触发Watcher时，客户端会收到一个数据包，说明znode已更改。 如果客户端与其中一个Zoo Keeper服务器之间的连接中断，客户端将收到本地通知。Watcher机制是ZooKeeper重要机制，是实现分布式锁和分布式配置平台的关键特性。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/zk_watcher.png?raw=true" height="400" width="400">	
</div>

<p align="center">
  <b>图 7 Watcher机制</b><br>
</p>

- **基本API**

   - create：创建一个znode
   - delete：删除一个znode
   - exists：检查znode是否存在
   - get data：获取znode数据
   - set data：把数据写入到znode
   - get children：获取znode下的子节点
   - sync：  等待数据同步
   
- **实现**

除请求处理器外，构成ZooKeeper服务的每个服务器都复制其自己的每个组件的副本。图8的replicated database是包含整个数据树的内存数据库。更新操作就会以日志形式写入到磁盘以备恢复使用，并且在数据被写入到内存数据库之前会先序列化到磁盘。

每个ZooKeeper server都能够为client提供服务。client只连接到一台server以提交请求。读请求结果由每个server的本地内存数据返回，更改服务状态和写请求由ZAB协议来处理。

来自client的所有写请求都被转发到一台服务器上，这台服务器叫leader，其他的zk server叫follower，follower接收来自leader的消息提议并同意消息传递。协议的消息层负责替换有问题的leader，并保持follower的状态与leader同步。

ZooKeeper使用一种自定义的原子消息传递协议(ZAB)。由于消息传递层是原子的，因为ZooKeeper可以保证所有zk server的本地副本最终一致性。当leader收到写请求时，它会计算出处理完写请求后的系统状态并将其转换为一个事务。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/zkcomponents.jpg?raw=true">	
</div>

<p align="center">
  <b>图 8 ZooKeeper实现示意图</b><br>
</p>

- **性能**

图9是ZooKeeper集群在不同读写请求比下的吞吐率，可以看到如果读请求越多的情况下，吞吐率越高，这是因为处理写请求需要同步状态。3台配置为2GHz的CPU和15K RPM的硬盘最高能处理的QPS将近8万。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/zkperfRW-3.2.jpg?raw=true">	
</div>

<p align="center">
  <b>图 9 ZooKeeper在不同读写请求币下的吞吐率</b><br>
</p>

## 4. ZAB协议

为了提高ZooKeeper性能和避免单点问题，ZooKeeper Server也是设计成分布式的。为了确保Server之间状态的一致性，引入了ZAB(ZooKeeper原子广播)协议。由于水平有限，本文只对ZAB协议做一个简略说明。ZAB协议由两种模式构成：
- 恢复模式
- 广播模式

当ZooKeeper服务启动或者是leader发生问题后，ZAB就进入恢复模式。当选举出leader，并且有过半的server已经与leader状态同步，ZAB将终止恢复模式并进入广播模式。

广播模式下请求处理过程如图10所示：

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/zkbroadcast.png?raw=true" height="300" width="500">	
</div>

<p align="center">
  <b>图 10 广播模式下数据流图</b><br>
</p>

广播模式使用FIFO(TCP)通道来通信，使用FIFO通道很容易使得消息保持有序，只要接收到消息就对消息进行处理，就能保证消息的顺序。

直到leader出现问题或者其follower数量不过半的时候，就会进入恢复模式。恢复模式开始的时候，会使用faster paxos算法选出新的leader。恢复模式的复杂性部分在于在特定时间内可能会有大量瞬时提案。为了保证就算leader出现问题，恢复模式下协议仍然能够正常运行，ZAB需要处理两种情况：
- 只要某条消息在一台机器上提交过，那么这条消息要在所有机器上提交。
- 如果某条消息未被提交过，那么这条消息总是要被忽略掉。

这两种情况分别如图11、12所示。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/zab_recover_case1.png?raw=true" height="300" width="500">	
</div>

<p align="center">
  <b>图 11 恢复模式场景1:消息不能丢弃，Server 1是leader，已经确认消息2被commited，尽管随后崩溃了，但仍然要保证所有消息2在所有服务器上commited</b><br>
</p>

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/zab_recover_case2_.png?raw=true?raw=true" height="400" width="500">	
</div>

<p align="center">
  <b>图 12 恢复模式场景2:消息必须忽略，Server 1是leader。尽管Server 1发送过P3，但是P3未被提交。而后Server 1又恢复，但是P3需要从它的log里删除掉</b><br>
</p>

本节只是简单介绍ZAB协议，关于协议的细节，如leader选举算法、状态同步方式等等内容待后续继续研究补充。

## 5. 基于ZooKeeper的分布式锁

在对ZooKeeper进一步了解后，我们继续通过ZooKeeper实现一个分布式锁，伪代码如下：

```
Lock // 加锁
1 n,err = create("${prefix}/lock", EPHEMERAL) // 尝试创建某个路径的临时类型znode
2 if err == nil, exit // 当节点存在或者create调用失败都会返回err，成功的话err为空,获得锁后就可以对临界变量进行操作
3 if exists(n, true) wait for watch event // 检查节点n是否仍然存在并设置watcher，如果存在则阻塞并等待锁，否则的话继续执行4
4 goto 1 // 返回1继续尝试创建

Unlock // 释放锁
1 delete(n)
```

锁的原则：在获取锁前尝试创建临时znode，如果成功就获得锁，否则的话可能会进入阻塞并设置watcher等待释放锁之后被唤醒。释放锁的过程很简单，删除节点。

虽然上面的锁在一般环境下能够正常工作，但是存在两点不足：
- 假设1000个客户端在尝试获得锁，由于每次只有1个客户端能够成功竞得锁，所以有999个客户端都处于阻塞状态导致严重的系统上下文切换。也就产生了[惊群效应](https://zh.wikipedia.org/wiki/%E6%83%8A%E7%BE%A4%E9%97%AE%E9%A2%98)。
- 只实现了互斥锁，在读多写少的情况下，效率很低。

利用ZooKeeper的znode的ephemeral和sequential特性很容易解决惊群效应，伪代码如下：

```
Lock // 加锁
1 n = create(l + "/lock-", EPHEMERAL|SEQUENTIAL) // 在路径l下创建路径前缀为lock-的临时、顺序特性znode
2 C = getChildren(l, false) // 获取路径l下的所有子节点
3 if n is lowest znode in C,exit  // 如果节点n是所有子节点里序号最小的节点，表示成功获取锁，接下来就可以对临界变量操作
4 p = znode in C ordered just before n // 否则的话，获取比节点n编号小1的节点p的路径
5 if exists(p, true) wait for watch event // 检查p是否存在并设置watcher，如果存在则阻塞并等待唤醒；否则的话继续循环加锁流程
6 goto 2

Unlock // 释放锁
1 delete(n)

Unlock // 释放锁
1 delete(n)
```

修改后的加锁过程与之前有点不一样，主要是创建顺序特性的znode，并且每个客户端在尝试获取锁的时候，只需要监测前一个节点状态来等待唤醒。这种方式下，每次释放锁的时候，只有唤醒一个客户端，避免唤醒大量客户端后又有大量客户端继续阻塞产生大量的上下文切换开销。

本文提供了基于go-zookeeper的客户端的无惊群效应锁实现，代码snippet如下，完整的示例请见[distributed_lock.go](https://github.com/berryjam/distributed-conf-demo/blob/master/src/distributed_lock.go)：

```
func lock() string {
	n, _ := zkHandler.Create(fmt.Sprintf("%s/lock-", "/didi"), []byte(" "), zk.FlagEphemeral|zk.FlagSequence, zk.WorldACL(zk.PermAll))
	for {
		children, _, _ := zkHandler.Children(fmt.Sprintf("%s", "/didi"))
		tmp := strings.Split(n, "-")
		nNum, _ := strconv.Atoi(formatSequenctialNodePath(tmp[1]))
		isLowestNode := true
		for _, child := range children {
			tmp = strings.Split(child, "-")
			childNum, _ := strconv.Atoi(formatSequenctialNodePath(tmp[1]))
			if nNum > childNum {
				isLowestNode = false
				break
			}
		}
		if isLowestNode {
			return n
		}
		p := fmt.Sprintf("%v", nNum-1)[1:]
		existed, _, ch, _ := zkHandler.ExistsW(p)
		if existed {
			<-ch
		}
	}
}

func unlock(node string) {
	_, stat, _ := zkHandler.Get(node)
	zkHandler.Delete(node, stat.Version)
}
```

因为sequential特性的znode的路径(代码里的tmp[1])是形如000xxxxx的字符串，所以不能直接用于比较大小，需要对tmp[1]作格式化，把开头的0去掉，再将剩下的字符串转为整数。

释放锁的过程就是把节点删除掉，非常简单。

**总结：通过一个ZooKeeper集群以及ZooKeeper提供的API，我们很容易实现了一个分布式锁以及搭建一个分布式配置中心，希望本文能够帮助大家了解ZooKeeper的基本概念以及用处。**

				
