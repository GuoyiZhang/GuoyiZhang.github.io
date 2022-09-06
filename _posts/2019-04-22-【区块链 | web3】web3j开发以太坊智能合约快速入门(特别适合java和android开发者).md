---
layout:     post
title:      【区块链 | web3】web3j开发以太坊智能合约快速入门(特别适合java和android开发者)
subtitle:   【区块链 | web3】web3j开发以太坊智能合约快速入门(特别适合java和android开发者)
date:       2019-04-22 15:26:51
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 区块链
    - ETH
    - 区块链零基础到实战教程
    - java
    - 以太坊
    - 智能合约
---

>【区块链 | web3】web3j开发以太坊智能合约快速入门(特别适合java和android开发者)

# 【区块链 | web3】web3j开发以太坊智能合约快速入门(特别适合java和android开发者)


# web3j简介

web3j是一个轻量级、高度模块化、响应式、类型安全的Java和Android类库提供丰富API，用于处理以太坊智能合约及与以太坊网络上的客户端(节点)进行集成。

可以通过它进行以太坊区块链的开发，而无需为你的应用平台编写集成代码。

# 可以快速启动dmeo示例

想要快速启动的话，有一个[Web3j demo示例项目](https://github.com/web3j/sample-project-gradle "Web3j demo示例项目")可用，演示了通过Web3j开发以太坊的许多核心特征，其中包括：

* 连接到以太网网络上的节点
* 加载一个以太坊钱包文件
* 将以太币从一个地址发送到另一个地址
* 向网络部署智能合约
* 从部署的智能合约中读取值
* 更新部署的智能合约中的值
* 查看由智能合约记录的事件

# web3j开发入门

首先将最新版本的web3j安装到项目中。

## Maven

**Java 8:**
<dependency> <groupId>org.web3j</groupId> <artifactId>core</artifactId> <version>3.4.0</version> </dependency>

**Android:**

<dependency> <groupId>org.web3j</groupId> <artifactId>core</artifactId> <version
