---
layout:     post
title:      【以太坊】Web3j通过合约地址监听transfer事件获取以太坊交易数据
subtitle:   【以太坊】Web3j通过合约地址监听transfer事件获取以太坊交易数据
date:       2019-08-08 16:54:59
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - ETH
    - 区块链
    - 区块链零基础到实战教程
    - 以太坊
    - java
    - 数字货币
---

>【以太坊】Web3j通过合约地址监听transfer事件获取以太坊交易数据

# 【以太坊】Web3j通过合约地址监听transfer事件获取以太坊交易数据


We3j
web3j是一个轻量级的Java库，用于在Ethereum网络上集成客户端（节点）。

核心特性

通过Java类型的JSON-RPC与Ethereum客户端进行交互
支持所有的JSON-RPC方法类型
支持所有Geth和Parity方法，用于管理账户和签署交易
同步或异步的发送客户端请求
可从Solidity ABI文件自动生成Java只能合约功能包
业务需求: 分析区块链转账记录，获取交易信息进一步分析。

原创代码，用web3j接入以太坊全节点，通过合约地址监听transfer事件，获取交易记录。

注意：注意版本问题。不同版本event的构造参数列表是不一样的。
<dependency>     <groupId>org.web3j</groupId>     <artifactId>core</artifactId>     <version>3.4.0</version> </dependency>

**GitHub**: [link](https://github.com/TianShengBingFeiNiuRen/block-chain-data/blob/master/src/main/java/com/andon/blockchaindata/service/MonitorTransferService.java "link"). 欢迎star

@Service public class MonitorTransferService { private static final
