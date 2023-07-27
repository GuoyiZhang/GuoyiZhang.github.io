---
layout: post
title: fabric联盟链高并发场景下如何提高TPS
date: 2019-05-09 13:44:00.000000000 +09:00
tags: fabric hyperledger 区块链 高并发 TPS
---


# fabric高并发场景下如何提高TPS

**Note. 本文主要描述在高并发场景下fabric的TPS（每秒钟交易数量，这里指的是有效的交易，不包括无效交易）为什么变得很低，如何提高TPS以及不同的提高TPS方式的优缺点。提高TPS的方式根本方式是避免交易冲突，只是避免交易冲突的思路不同。**

**避免冲突的方式可以分为2种类型，一种是使用高效的chaincode数据模型，完全避免交易发生冲突，但局限性比较大；一种是在依赖分布式锁或者MVCC等机制避免交易发生冲突，如果在chaincode执行过程中就能检测到冲突并终止交易，能够忽略掉后面交易校验、读写账本、同步账本等等流程的话，就能够大大减少交易耗时从而间接提高TPS。**

**这两种避免冲突的方式有各自的优缺点，开发者可以根据实际的业务场景选择不同的方式来提高TPS。而后者又可以细分为阻塞式和分阻塞式两种避免交易冲突方式。**

- [1. fabric高并发场景下的交易冲突](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-05-07-fabric%E8%81%94%E7%9B%9F%E9%93%BE%E9%AB%98%E5%B9%B6%E5%8F%91%E5%9C%BA%E6%99%AF%E4%B8%8B%E5%A6%82%E4%BD%95%E6%8F%90%E9%AB%98TPS.md#1-fabric%E9%AB%98%E5%B9%B6%E5%8F%91%E5%9C%BA%E6%99%AF%E4%B8%8B%E7%9A%84%E4%BA%A4%E6%98%93%E5%86%B2%E7%AA%81)

- [2. 基于高效chaincode数据模型的避免交易冲突方式](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-05-07-fabric%E8%81%94%E7%9B%9F%E9%93%BE%E9%AB%98%E5%B9%B6%E5%8F%91%E5%9C%BA%E6%99%AF%E4%B8%8B%E5%A6%82%E4%BD%95%E6%8F%90%E9%AB%98TPS.md#2-%E5%9F%BA%E4%BA%8E%E9%AB%98%E6%95%88chaincode%E6%95%B0%E6%8D%AE%E6%A8%A1%E5%9E%8B%E7%9A%84%E9%81%BF%E5%85%8D%E4%BA%A4%E6%98%93%E5%86%B2%E7%AA%81%E6%96%B9%E5%BC%8F)

- [3. 基于阻塞和非阻塞式的避免交易冲突方式](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-05-07-fabric%E8%81%94%E7%9B%9F%E9%93%BE%E9%AB%98%E5%B9%B6%E5%8F%91%E5%9C%BA%E6%99%AF%E4%B8%8B%E5%A6%82%E4%BD%95%E6%8F%90%E9%AB%98TPS.md#3-%E5%9F%BA%E4%BA%8E%E9%98%BB%E5%A1%9E%E5%92%8C%E9%9D%9E%E9%98%BB%E5%A1%9E%E5%BC%8F%E7%9A%84%E9%81%BF%E5%85%8D%E4%BA%A4%E6%98%93%E5%86%B2%E7%AA%81%E6%96%B9%E5%BC%8F)

	- [3.1. 使用分布式锁阻塞型同步机制](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-05-07-fabric%E8%81%94%E7%9B%9F%E9%93%BE%E9%AB%98%E5%B9%B6%E5%8F%91%E5%9C%BA%E6%99%AF%E4%B8%8B%E5%A6%82%E4%BD%95%E6%8F%90%E9%AB%98TPS.md#31-%E4%BD%BF%E7%94%A8%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81%E9%98%BB%E5%A1%9E%E5%9E%8B%E5%90%8C%E6%AD%A5%E6%9C%BA%E5%88%B6)

	- [3.2. 利用MVCC非阻塞型的方式](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-05-07-fabric%E8%81%94%E7%9B%9F%E9%93%BE%E9%AB%98%E5%B9%B6%E5%8F%91%E5%9C%BA%E6%99%AF%E4%B8%8B%E5%A6%82%E4%BD%95%E6%8F%90%E9%AB%98TPS.md#32-%E5%88%A9%E7%94%A8mvcc%E9%9D%9E%E9%98%BB%E5%A1%9E%E5%9E%8B%E7%9A%84%E6%96%B9%E5%BC%8F)

- [4. 参考资料](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-05-07-fabric%E8%81%94%E7%9B%9F%E9%93%BE%E9%AB%98%E5%B9%B6%E5%8F%91%E5%9C%BA%E6%99%AF%E4%B8%8B%E5%A6%82%E4%BD%95%E6%8F%90%E9%AB%98TPS.md#4-%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)



## 1. fabric高并发场景下的交易冲突

### 1.1 实验准备

为了描述fabric在高并发场景下的交易冲突是怎样的，我们使用[fabric-samples/first-network](https://github.com/hyperledger/fabric-samples/tree/master/first-network)作为示例。

在运行first-network之前，参考[官方文档](https://hyperledger-fabric.readthedocs.io/en/latest/install.html)安装好必要的二进制文件和镜像。

- git clone git@github.com:hyperledger/fabric-samples.git
- cd fabric-samples/first-network
- 生成fabric网络材料：./byfn.sh generate
- 启动fabric网络：./byfn.sh up，运行没问题的话，会在first-network上安装好chaincode_example02.go链代码，这个链码逻辑比较但简单，可以看作是存储用户的金额，并且支持任意两个用户间的转账，也支持单个用户的查询。运行脚本后，会把a、b的值初始化为100、200，并且调用一次a向b转账，此刻a的金额为90，b的金额为210。
- 数据对齐：为了让a、b的数值取整方便后面实验观察，在另外一个终端执行docker exec -it cli bash，进入容器后调用链代码，b向a转账：`peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n mycc --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"Args":["invoke","b","a","10"]}'`


### 1.2 fabric交易冲突

接下来进行10次并发转账操作，从a向b的账户每次转账10元。正常情况下，peer容器的日志应该正常输出，并且结束后a的账户余额应该为0，而b的账户余额应该为300。

```
#!/bin/bash


for ((i=0;i<10;i++))
do
    peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n mycc --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"Args":["invoke","a","b","10"]}' &
done
```

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/2019-05-02-high-throughout/result.png?raw=true" >	
</div>

<p align="center">
  <b>图 1 并发执行10次转账结果图</b><br>
</p>

**可以看到，a的值为90，b的值为210，实际有效转账次数为1次**！也就是说10笔转账交易里，只有一笔是有效的，可见高并发情况下，fabric的TPS是如此之低。

图2是peer容器的日志，从日志来看，在这10笔交易里有9笔交易的读写集发生冲突，导致交易被标记为无效交易，a和b的世界状态也只改变了1次。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/2019-05-02-high-throughout/mmvc_read_conflict.jpeg?raw=true" >
</div>

<p align="center">
  <b>图 2 交易读写集冲突日志图</b><br>
</p>

### 1.3 为什么转账会失败并且会发生fabric交易冲突

fabric为了解决双花问题，引入读写集和MVCC机制，本节内容翻译自官方文档[Read-Write set semantics](https://hyperledger-fabric.readthedocs.io/en/latest/readwrite.html)。

#### 1.3.1 交易模拟执行和读写集

在```endorser```节点模拟执行交易时，会为交易准备读写集。```读集合```包含这次交易模拟执行中所包含的一系列唯一key还有key最近提交的版本号。而```写集合```则包含交易会进行写操作的一系列key（可能会跟读集合有重叠）和key要写入的新值。如果交易执行的更新是删除key，则为key设置删除标记（代替新值）。

此外，如果交易为key多次写入值，则仅保留最后写入的值。 此外，如果交易读取key的值，则即使交易在发出读取之前更新了键的值，也会返回已提交状态的值。 换句话说，不支持Read-your-writes语义。

如前所述，key的版本仅记录在读集合中; 写集合只包含交易设置的唯一key列表及其最新值。

可以有各种方案来实现版本。 版本控制方案的最低要求是为给定key生成非重复标识符。 例如，对于版本使用单调递增的数字可以是一种这样的方案。 在当前实现中，fabric使用基于区块链高度的版本控制方案，其中已提交的交易的高度（交易所在块的高度）被用作交易修改的所有key的最新版本。 在此方案中，交易的高度由元组表示（txNumber是块内交易的高度）。 与增量数字方案相比，该方案具有许多优点 - 主要是，它可以使其他组件如世界状态数据库，交易模拟执行和验证中，能够进行有效的设计选择。

以下是通过模拟假设的交易准备的示例读写集的描述。 为简单起见，在实描述中，使用增量数而不是交易的高度来表示版本。

```
<TxReadWriteSet>
  <NsReadWriteSet name="chaincode1">
    <read-set>
      <read key="K1", version="1">
      <read key="K2", version="1">
    </read-set>
    <write-set>
      <write key="K1", value="V1"
      <write key="K3", value="V2"
      <write key="K4", isDelete="true"
    </write-set>
  </NsReadWriteSet>
<TxReadWriteSet>
```

#### 1.3.2 使用读写集验证交易和更新世界状态

**committer节点**会使用读写集里的读集合验证交易的合法性，使用读写集的写集合来更新相关key的版本和值。

在交易验证阶段，如果交易的读集合里面的所有key的版本与发生交易时key世界状态版本都一致的话，那么这笔交易就认为是合法的（这里在这笔交易所在的块并在该交易之前的交易都已经提交）。如果读写集包含一个或者多个query-info，则执行附加验证。

该附加验证应该确保在query-info中的结果的超范围（即，范围的并集）中没有插入/删除/更新key。换句话说，如果我们在提交状态的验证期间重新执行任何范围查询（在模拟期间执行的交易），它应该产生与模拟执行交易所观察到的结果相同的结果。此检查确保如果交易在提交期间观察到幻读记录，则应将交易标记为无效。注意，该幻读保护仅限于范围查询（即，链代码中的```GetStateByRange```函数），并且尚未针对其他查询（即，链代码中的```GetQueryResult```函数）实现。其他查询存在幻读风险，因此应仅用于未提交排序的只读交易，除非应用程序可以保证模拟和验证/提交时间之间结果集的稳定性。

如果交易通过了有效性检查，则committer节点使用写集来更新世界状态。 在更新阶段，对于写集中存在的每个key，相同key的世界状态中的值被设置为写集中指定的值。 此外，世界状态中的key版本被更改以反映最新版本。

#### 1.3.3 模拟执行和验证示例

本节通过示例场景帮助理解读写集的语义。 出于该示例的目的，世界状态中的键```k```的存在由元组```（k，ver，val）```表示，其中```ver```是具有```val```作为其值的键```k```的最新版本。

现在，考虑一组五个交易```T1，T2，T3，T4和T5```，所有交易都在世界状态的同一快照上进行模拟执行。 以下代码段显示了模拟交易的世界状态的快照以及每个交易执行的读写活动的顺序。

```
World state: (k1,1,v1), (k2,1,v2), (k3,1,v3), (k4,1,v4), (k5,1,v5)
T1 -> Write(k1, v1'), Write(k2, v2')
T2 -> Read(k1), Write(k3, v3')
T3 -> Write(k2, v2'')
T4 -> Write(k2, v2'''), read(k2)
T5 -> Write(k6, v6'), read(k5)
```

现在，假设这些交易按T1，...，T5的顺序排序（可以包含在单个块或不同的块中）。

1. ```T1```通过了验证，因为T1没有进行任何读操作。此外，```k1```和```k2```的世界状态元祖被更新为```(k1,2,v1'), (k2,2,v2')```。

2. ```T2```没有通过验证，因为T2读了一个key ```k1```。而```k1```在前一笔交易```T1```已经被修改过。

3. ```T3```通过了验证，因为T3没有进行读操作。此外，```k2```的世界状态元祖被更新为```(k2,3,v2'')```。

4. ```T4```没有通过验证，因为T4读了一个key```k2```。而```k2```在前面的```T1```交易中已经被更新过。

5. ```T5```通过了验证，因为T5读了一个key```k5```，但是```k5```在前面的交易中没有被修改过。


所以回到我们上面的10次并发转账例子，每次转账会涉及到读写a、b世界状态，分析如下，所以只有最开始的第一笔交易T1能够通过验证，其他交易都被标记为invalid，并没有成功修改a、b的世界状态。对用户来说，后面9次调用都失败了，需要重试，因为TPS大大降低。

```
World state: (a,100,a1) b(b,200,b1)
T1 -> Read(a), Write(a, a1'), Read(b), Write(b, b1')，通过验证，因为T1是第一次读写a、b的交易，此刻a、b的版本与执行交易前的版本一致
T2 -> Read(a), Write(a, a1''), Read(b), Write(b, b1'') 没有通过验证，因为T2会读取a、b的值，而a、b的值在T1已经被修改过。
...
后面8笔交易分析与T2相同
```

## 2. 基于高效chaincode数据模型的避免交易冲突方式



## 3. 基于阻塞和非阻塞式的避免交易冲突方式



### 3.1. 使用分布式锁阻塞型同步机制


### 3.2. 利用MVCC非阻塞型的方式

## 4. 参考资料
