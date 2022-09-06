---
layout:     post
title:      【区块链 | IPFS】IPFS使用教程 | java对接IPFS的SDK
subtitle:   【区块链 | IPFS】IPFS使用教程 | java对接IPFS的SDK
date:       2022-04-03 23:36:01
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链零基础到实战教程
    - Java
    - 区块链
    - 区块链
    - 去中心化
    - 以太坊
    - ipfs
---

>【区块链 | IPFS】IPFS使用教程 | java对接IPFS的SDK

# 【区块链 | IPFS】IPFS使用教程 | java对接IPFS的SDK


# 首先，引入IPFS的包

## maven方式

<repositories> <repository> <id>jitpack.io</id> <url>https://jitpack.io</url> </repository> </repositories> <dependency> <groupId>com.github.ipfs</groupId> <artifactId>java-ipfs-api</artifactId> <version>1.3.3</version> </dependency>

如果上述1.3.3版本的无法下载依赖包，可尝试1.2.2版本。
这个时候如果api包导入成功，但是使用的时候提示相关的依赖无法import，可以手动加入下面的依赖（都是IPFS的内部依赖包）

<dependency> <groupId>com.github.multiformats</groupId> <artifactId>java-multihash</artifactId> <version>v1.3.0</version> </dependency> <dependency> <groupId>com.github.multiformats</groupId> <artifactId>java-multibase</artifactId> <version>v1.1.0</version> </dependency> <dependency> <groupI
